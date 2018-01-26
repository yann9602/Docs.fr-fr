---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: "Didacticiel : Mise en route avec SignalR 1.x | Documents Microsoft"
author: pfletcher
description: "Utilisez ASP.NET SignalR pour générer une application de conversation en temps réel dans une page HTML."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2013
ms.topic: article
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: ce4953a0abf64af28ef4dbc5a62bb2d989343d99
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="tutorial-getting-started-with-signalr-1x"></a>Didacticiel : Mise en route avec SignalR 1.x
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

> Ce didacticiel montre comment utiliser SignalR pour créer une application de conversation en temps réel. Vous ajoutez SignalR à l’application web ASP.NET vide et créer une page HTML pour envoyer et afficher les messages.


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel présente le développement de SignalR en montrant comment créer une application simple conversation basée sur navigateur. Vous ajoute la bibliothèque SignalR à l’application web ASP.NET vide, créez une classe de concentrateur pour envoyer des messages aux clients et créer une page HTML qui permet aux utilisateurs d’envoyer et recevoir des messages de conversation. Pour obtenir un didacticiel similaire qui montre comment créer une application de conversation dans MVC 4 à l’aide d’une vue MVC, consultez [prise en main SignalR et MVC 4](index.md).

> [!NOTE]
> Ce didacticiel utilise la version (1.x) de SignalR. Pour plus d’informations sur les modifications entre SignalR 1.x et 2.0, consultez [la mise à niveau de SignalR 1.x projets](../releases/upgrading-signalr-1x-projects-to-20.md).

SignalR est une bibliothèque de .NET open source pour la création d’applications web qui requièrent une intervention de l’utilisateur en direct ou mises à jour des données en temps réel. Exemples applications sociales, jeux multi-utilisateur, météo de collaboration et de news, entreprise ou des applications de mise à jour financière. Il s’agit souvent d’applications en temps réel.

SignalR simplifie le processus de création d’applications en temps réel. Elle inclut une bibliothèque de serveur ASP.NET et d’une bibliothèque cliente de JavaScript pour le rendre plus facile à gérer les connexions client-serveur et d’envoyer des mises à jour de contenu aux clients. Vous pouvez ajouter la bibliothèque SignalR pour une application ASP.NET existante pour bénéficier de fonctionnalités en temps réel.

Le didacticiel présente les tâches de développement SignalR suivantes :

- Ajout de la bibliothèque de SignalR pour une application web ASP.NET.
- Création d’une classe de concentrateur pour transmettre le contenu aux clients.
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

Cette section montre comment créer une application web ASP.NET vide, ajoutez SignalR et créer l’application de la conversation.

Conditions préalables :

- Visual Studio 2010 SP1 ou 2012. Si vous n’avez pas Visual Studio, consultez [téléchargements ASP.NET](https://www.asp.net/downloads) pour obtenir le Visual Studio 2012 Express outil de développement gratuit.
- [Microsoft ASP.NET et Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941). Pour Visual Studio 2012, ce programme d’installation ajoute de nouvelles fonctionnalités d’ASP.NET, y compris les modèles de SignalR pour Visual Studio. Pour Visual Studio 2010 SP1, un programme d’installation n’est pas disponible, mais vous pouvez terminer le didacticiel en installant le package NuGet de SignalR, comme décrit dans les étapes d’installation.

Les étapes suivantes utilisent Visual Studio 2012 pour créer une Application de Web ASP.NET vide et ajouter la bibliothèque SignalR :

1. Dans Visual Studio, créez une Application de Web ASP.NET vide.

    ![Créer le site web vide](tutorial-getting-started-with-signalr/_static/image2.png)
2. Ouvrez le **Package Manager Console** en sélectionnant **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package**. Entrez la commande suivante dans la fenêtre de console :

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    Cette commande installe la version la plus récente de SignalR 1.x.
3. Dans **l’Explorateur de solutions**, cliquez sur le projet, sélectionnez **ajouter | Classe**. Nommez la nouvelle classe **ChatHub**.
4. Dans **l’Explorateur de solutions** développez le nœud Scripts. Bibliothèques de scripts de jQuery et SignalR sont visibles dans le projet.

    ![Références de bibliothèque](tutorial-getting-started-with-signalr/_static/image3.png)
5. Remplacez le code dans le **ChatHub** classe par le code suivant.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Un nouvel élément**. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez **classe d’Application globale** et cliquez sur **ajouter**.

    ![Ajouter global](tutorial-getting-started-with-signalr/_static/image4.png)
7. Ajoutez le code suivant `using` instructions après avoir fourni `using` instructions dans la classe Global.asax.cs.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. Ajoutez la ligne suivante de code dans la `Application_Start` méthode de la classe globale pour inscrire l’itinéraire par défaut pour les concentrateurs SignalR.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Un nouvel élément**. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez Html Page, cliquez sur **ajouter**.
10. Dans **l’Explorateur de solutions**, avec le bouton droit de la page HTML que vous venez de créer, cliquez sur **définir comme Page de démarrage**.
11. Remplacez le code par défaut dans la page HTML par le code suivant.

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. **Enregistrer tous les** pour le projet.

<a id="run"></a>

## <a name="run-the-sample"></a>Exécuter l’exemple

1. Appuyez sur F5 pour exécuter le projet en mode débogage. Charge de la page HTML dans une instance du navigateur et les invite à entrer un nom d’utilisateur.

    ![Entrer un nom d'utilisateur](tutorial-getting-started-with-signalr/_static/image5.png)
2. Entrez un nom d’utilisateur.
3. Copiez l’URL de la ligne d’adresse du navigateur et l’utiliser pour ouvrir les deux instances du navigateur plus. Dans chaque instance du navigateur, entrez un nom d’utilisateur unique.
4. Dans chaque instance du navigateur, ajoutez un commentaire, puis cliquez sur **envoyer**. Les commentaires doivent s’afficher dans toutes les instances du navigateur.

    > [!NOTE]
    > Cette application de conversation simple ne conserve pas le contexte de la discussion sur le serveur. Le concentrateur diffuse des commentaires à tous les utilisateurs actuels. Les utilisateurs qui participer à la conversation ultérieurement voient des messages ajoutés à partir du moment qu'ils se connectent.

    La capture d’écran suivante montre l’application de la conversation en cours d’exécution dans les trois instances de navigateur, qui sont mis à jour lorsqu’une instance envoie un message :

    ![Navigateurs de conversation](tutorial-getting-started-with-signalr/_static/image6.png)
5. Dans **l’Explorateur de solutions**, inspecter la **Documents de Script** nœud pour l’application en cours d’exécution. Il existe un fichier de script nommé **concentrateurs** que la bibliothèque SignalR génère dynamiquement lors de l’exécution. Ce fichier gère la communication entre le script jQuery et le code côté serveur.

    ![Script de concentrateur généré](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a>Examinez le Code

L’application de la conversation SignalR illustre deux tâches de développement SignalR base : création d’un hub en tant que l’objet principal de coordination sur le serveur et à l’aide de la bibliothèque jQuery SignalR pour envoyer et recevoir des messages.

### <a name="signalr-hubs"></a>Concentrateurs SignalR

Dans l’exemple de code la **ChatHub** classe dérive de la **Microsoft.AspNet.SignalR.Hub** classe. Dérivation à partir de la **Hub** classe est une méthode utile pour générer une application SignalR. Vous pouvez créer des méthodes publiques sur votre classe de concentrateur et puis accéder à ces méthodes en les appelant à partir de scripts jQuery dans une page web.

Dans le code de la conversation, les clients appellent le **ChatHub.Send** méthode pour envoyer un message. Le concentrateur à son tour envoie le message à tous les clients en appelant **Clients.All.broadcastMessage**.

Le **envoyer** méthode illustre plusieurs concepts de hub :

- Déclarer des méthodes publiques sur un concentrateur, afin que les clients peuvent appeler les.
- Utilisez le **Microsoft.AspNet.SignalR.Hub.Clients** propriétés dynamiques pour accéder à tous les clients connectés à ce concentrateur.
- Appeler une fonction de jQuery sur le client (telles que le `broadcastMessage` (fonction)) pour mettre à jour des clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR et jQuery

La page HTML dans l’exemple de code montre comment utiliser la bibliothèque jQuery SignalR pour communiquer avec un concentrateur SignalR. Les tâches essentielles dans le code déclarez un proxy pour référencer le concentrateur, déclarez une fonction qui peut appeler le serveur pour envoyer aux clients et démarrage d’une connexion pour envoyer des messages au concentrateur.

Le code suivant déclare un proxy pour un concentrateur.

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> Dans jQuery la référence à la classe de serveur et de ses membres est en casse mixte. L’exemple de code fait référence à c# **ChatHub** jQuery en tant que classe **chatHub**.


Le code suivant est la façon dont vous créez une fonction de rappel dans le script. La classe de concentrateur sur le serveur appelle cette fonction pour transmettre les mises à jour à chaque client. Les deux lignes que HTML encoder le contenu avant de les afficher sont facultatifs et affichent un moyen simple pour empêcher l’injection de script.

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

Le code suivant montre comment ouvrir une connexion avec le concentrateur. Le code démarre la connexion et qu’il passe ensuite une fonction pour gérer l’événement de clic sur le **envoyer** bouton dans la page HTML.

> [!NOTE]
> Cette approche garantit que la connexion est établie avant l’exécution du Gestionnaire d’événements.


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris que SignalR est une infrastructure pour la génération d’applications web en temps réel. Vous avez également appris à plusieurs tâches de développement SignalR : comment ajouter SignalR pour une application ASP.NET, la création d’une classe de concentrateur et comment envoyer et recevoir des messages à partir du concentrateur.

Vous pouvez mettre l’exemple d’application dans ce didacticiel ou d’autres applications SignalR disposition sur Internet en les déployant sur un fournisseur d’hébergement. Microsoft propose d’hébergement web gratuit pour jusqu'à 10 sites web dans un [compte d’évaluation de Windows Azure](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604). Pour une procédure pas à pas sur la façon de déployer l’exemple d’application SignalR, consultez [publier le SignalR exemple Getting Started comme un Site Web de Windows Azure](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx). Pour plus d’informations sur la façon de déployer un projet web Visual Studio sur un Site Web de Windows Azure, consultez [déploiement d’une Application ASP.NET à un Site Web de Windows Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet). (Remarque : le transport WebSocket est actuellement pas en charge pour les Sites Web Windows Azure. Transport WebSocket lorsque n’est pas disponible, SignalR utilise les autres transports disponibles comme décrit dans la section des Transports de la [Introduction à SignalR rubrique](index.md).)

Pour en savoir plus avancées concepts de développements SignalR, visitez les sites suivants pour le code source de SignalR et ressources :

- [Projet SignalR](http://signalr.net)
- [SignalR Github et exemples](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

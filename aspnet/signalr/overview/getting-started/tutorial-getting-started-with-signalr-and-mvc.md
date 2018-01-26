---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: "Didacticiel : Mise en route avec SignalR 2 et MVC 5 | Documents Microsoft"
author: pfletcher
description: "Ce didacticiel montre comment utiliser ASP.NET SignalR 2 pour créer une application de conversation en temps réel. Vous allez ajouter SignalR à une application MVC 5 et créer une vue de la conversation..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.openlocfilehash: ca0471114da7363c5df9d459308708e7ab4f8b84
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="tutorial-getting-started-with-signalr-2-and-mvc-5"></a>Didacticiel : Mise en route avec SignalR 2 et MVC 5
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-c366b2f3)

> Ce didacticiel montre comment utiliser ASP.NET SignalR 2 pour créer une application de conversation en temps réel. Vous ajoutez SignalR à une application MVC 5 et créer une vue de conversation pour envoyer et afficher les messages. 
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - MVC 5
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

Ce didacticiel vous présente au développement d’applications web en temps réel avec ASP.NET SignalR 2 et ASP.NET MVC 5. Ce didacticiel utilise le même code d’application de conversation que la [SignalR prise en main de didacticiel](tutorial-getting-started-with-signalr.md), mais vous montre comment l’ajouter à une application MVC 5.

Dans cette rubrique, vous allez apprendre les tâches de développement SignalR suivantes :

- Ajout de la bibliothèque de SignalR à une application MVC 5.
- Création de hub et les classes de démarrage OWIN pour transmettre le contenu aux clients.
- À l’aide de la bibliothèque jQuery SignalR dans une page web pour envoyer des messages et d’afficher les mises à jour à partir du concentrateur.

La capture d’écran suivante montre l’application de conversation terminée en cours d’exécution dans un navigateur.

![Instances de conversation](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

Sections :

- [Le projet](#setup)
- [Exécuter l’exemple](#run)
- [Examinez le Code](#code)
- [Étapes suivantes](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a>Le projet

Conditions préalables :

- Visual Studio 2013. Si vous n’avez pas Visual Studio, consultez [téléchargements ASP.NET](https://www.asp.net/downloads) pour obtenir le Visual Studio 2013 Express outil de développement gratuit.

Cette section montre comment créer une application ASP.NET MVC 5, ajoutez la bibliothèque SignalR et créer l’application de la conversation.

1. Dans Visual Studio, créez une application c# ASP.NET qui cible .NET Framework 4.5, nommez-le SignalRChat, cliquez sur OK.

    ![Créer le web](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)
2. Dans le `New ASP.NET Project` boîte de dialogue, puis sélectionnez **MVC**, puis cliquez sur **modifier l’authentification**.

    ![Créer le web](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)
3. Sélectionnez **aucune authentification** dans les **modifier l’authentification** boîte de dialogue, puis cliquez sur **OK**.

    ![Ne sélectionnez aucune authentification](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

    > [!NOTE]
    > Si vous sélectionnez un fournisseur d’authentification pour votre application, un `Startup.cs` classe sera créé pour vous, vous ne devez pas créer votre propre `Startup.cs` classe à l’étape 10 ci-dessous.
4. Cliquez sur **OK** dans les **nouveau projet ASP.NET** boîte de dialogue.
5. Ouvrez le **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package** et exécutez la commande suivante. Cette étape ajoute au projet un ensemble de fichiers de script et les références d’assembly que vous activez la fonctionnalité de SignalR.

    `install-package Microsoft.AspNet.SignalR`
6. Dans **l’Explorateur de solutions**, développez le dossier Scripts. Notez que les bibliothèques de scripts pour SignalR ont été ajoutés au projet.

    ![Dossier de scripts](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)
7. Dans **l’Explorateur de solutions**, cliquez sur le projet, sélectionnez **ajouter | Nouveau dossier**, et ajouter un nouveau dossier nommé **concentrateurs**.
8. Cliquez sur le **concentrateurs** dossier, cliquez sur **ajouter | Un nouvel élément**, sélectionnez le **Visual C# | Web | SignalR** nœud dans le **installé** volet, sélectionnez **classe de concentrateur SignalR (v2)** dans le volet central et créer un nouveau concentrateur nommé **ChatHub.cs**. Vous allez utiliser cette classe comme un concentrateur SignalR serveur qui envoie des messages à tous les clients.

    ![Créer le nouveau concentrateur](tutorial-getting-started-with-signalr-and-mvc/_static/image6.png)
9. Remplacez le code dans le **ChatHub** classe par le code suivant.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]
10. Créer une nouvelle classe nommée Startup.cs. Modifier le contenu du fichier à ce qui suit.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]
11. Modifier la `HomeController` classe trouvé dans **Controllers/HomeController.cs** et ajoutez la méthode suivante à la classe. Cette méthode retourne le **Chat** vue que vous allez créer dans une étape ultérieure.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]
12. Cliquez sur le **Views/Home** dossier, puis sélectionnez **ajouter... | Vue**.
13. Dans le **ajouter une vue** boîte de dialogue, nom de la nouvelle vue **Chat**.

    ![Ajouter une vue](tutorial-getting-started-with-signalr-and-mvc/_static/image7.png)
14. Remplacez le contenu de **Chat.cshtml** avec le code suivant.

    > [!IMPORTANT]
    > Lorsque vous ajoutez SignalR et autres bibliothèques de script à votre projet Visual Studio, le Gestionnaire de Package peut installer une version du fichier de script SignalR qui est plus récente que la version indiquée dans cette rubrique. Assurez-vous que la référence de script dans votre code correspond à la version de la bibliothèque de script installée dans votre projet.

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]
15. **Enregistrer tous les** pour le projet.

<a id="run"></a>

## <a name="run-the-sample"></a>Exécuter l’exemple

1. Appuyez sur F5 pour exécuter le projet en mode débogage.
2. Dans la ligne d’adresse du navigateur, ajoutez **/home/chat** à l’URL de la page par défaut pour le projet. Chargement de la page de la conversation dans une instance du navigateur et les invite à entrer un nom d’utilisateur.

    ![Entrer un nom d'utilisateur](tutorial-getting-started-with-signalr-and-mvc/_static/image8.png)
3. Entrez un nom d’utilisateur.
4. Copiez l’URL de la ligne d’adresse du navigateur et l’utiliser pour ouvrir les deux instances du navigateur plus. Dans chaque instance du navigateur, entrez un nom d’utilisateur unique.
5. Dans chaque instance du navigateur, ajoutez un commentaire, puis cliquez sur **envoyer**. Les commentaires doivent s’afficher dans toutes les instances du navigateur.

    > [!NOTE]
    > Cette application de conversation simple ne conserve pas le contexte de la discussion sur le serveur. Le concentrateur diffuse des commentaires à tous les utilisateurs actuels. Les utilisateurs qui participer à la conversation ultérieurement voient des messages ajoutés à partir du moment qu'ils se connectent.
6. La capture d’écran suivante montre l’application de conversation en cours d’exécution dans un navigateur.

    ![Navigateurs de conversation](tutorial-getting-started-with-signalr-and-mvc/_static/image9.png)
7. Dans **l’Explorateur de solutions**, inspecter la **Documents de Script** nœud pour l’application en cours d’exécution. Ce nœud est visible en mode débogage si vous utilisez Internet Explorer comme navigateur. Il existe un fichier de script nommé **concentrateurs** que la bibliothèque SignalR génère dynamiquement lors de l’exécution. Ce fichier gère la communication entre le script jQuery et le code côté serveur. Si vous utilisez un navigateur autre qu’Internet Explorer, vous pouvez également accéder à la dynamique **concentrateurs** fichier en parcourant directement, par exemple http://mywebsite/signalr/hubs.

<a id="code"></a>

## <a name="examine-the-code"></a>Examinez le Code

L’application de la conversation SignalR illustre deux tâches de développement SignalR base : création d’un hub en tant que l’objet principal de coordination sur le serveur et à l’aide de la bibliothèque jQuery SignalR pour envoyer et recevoir des messages.

### <a name="signalr-hubs"></a>Concentrateurs SignalR

Dans l’exemple de code la **ChatHub** classe dérive de la **Microsoft.AspNet.SignalR.Hub** classe. Dérivation à partir de la **Hub** classe est une méthode utile pour générer une application SignalR. Vous pouvez créer des méthodes publiques sur votre classe de concentrateur et puis accéder à ces méthodes en les appelant à partir de scripts dans une page web.

Dans le code de la conversation, les clients appellent le **ChatHub.Send** méthode pour envoyer un message. Le concentrateur à son tour envoie le message à tous les clients en appelant **Clients.All.addNewMessageToPage**.

Le **envoyer** méthode illustre plusieurs concepts de hub :

- Déclarer des méthodes publiques sur un concentrateur, afin que les clients peuvent appeler les.
- Utilisez le **Microsoft.AspNet.SignalR.Hub.Clients** propriété pour accéder à tous les clients connectés à ce concentrateur.
- Appeler une fonction sur le client (telles que le `addNewMessageToPage` (fonction)) pour mettre à jour des clients.

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a>SignalR et jQuery

Le **Chat.cshtml** afficher le fichier dans l’exemple de code montre comment utiliser la bibliothèque jQuery SignalR pour communiquer avec un concentrateur SignalR. Les tâches essentielles dans le code créez une référence pour le proxy généré automatiquement pour le concentrateur, déclarer une fonction que le serveur peut appeler pour envoyer aux clients et le démarrage d’une connexion pour envoyer des messages au concentrateur.

Le code suivant déclare une référence à un concentrateur proxy.

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> Dans JavaScript, la référence à la classe de serveur et de ses membres est en casse mixte. L’exemple de code fait référence à c# **ChatHub** classe en JavaScript en tant que **chatHub**. Si vous souhaitez faire référence à la `ChatHub` classe dans jQuery avec Pascal conventionnel casse comme vous le feriez dans c#, modifier le fichier de classe ChatHub.cs. Ajouter un `using` instruction pour faire référence à la `Microsoft.AspNet.SignalR.Hubs` espace de noms. Ajoutez ensuite la `HubName` attribut le `ChatHub` de classe, par exemple `[HubName("ChatHub")]`. Enfin, mettre à jour votre référence de jQuery à la `ChatHub` classe.


Le code suivant montre comment créer une fonction de rappel dans le script. La classe de concentrateur sur le serveur appelle cette fonction pour transmettre les mises à jour à chaque client. L’appel facultatif à la `htmlEncode` affiche la fonction HTML permet de coder le contenu du message avant de les afficher dans la page, comme un moyen d’empêcher l’injection de script.

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

Le code suivant montre comment ouvrir une connexion avec le concentrateur. Le code démarre la connexion et qu’il passe ensuite une fonction pour gérer l’événement de clic sur le **envoyer** bouton dans la page de la conversation.

> [!NOTE]
> Cette approche garantit que la connexion est établie avant l’exécution du Gestionnaire d’événements.


[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris que SignalR est une infrastructure pour la génération d’applications web en temps réel. Vous avez également appris à plusieurs tâches de développement SignalR : comment ajouter SignalR pour une application ASP.NET, la création d’une classe de concentrateur et comment envoyer et recevoir des messages à partir du concentrateur.

Pour une procédure pas à pas sur la façon de déployer l’exemple d’application SignalR dans Azure, consultez [à l’aide de SignalR avec les applications Web dans Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Pour plus d’informations sur la façon de déployer un projet web Visual Studio sur un Site Web de Windows Azure, consultez [créer une application web ASP.NET dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

Pour en savoir plus avancées concepts de développements SignalR, visitez les sites suivants pour le code source de SignalR et ressources :

- [Projet SignalR](http://signalr.net)
- [SignalR Github et exemples](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: "Didacticiel : En temps réel de haute fréquence avec SignalR 2 | Documents Microsoft"
author: pfletcher
description: "Ce didacticiel montre comment créer une application web qui utilise ASP.NET SignalR pour fournir des fonctionnalités de messagerie haute fréquence. Messagerie de haute fréquence..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: ab051b2ab85d1aac1e7179f342f22f470b1d1cc7
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="tutorial-high-frequency-realtime-with-signalr-2"></a>Didacticiel : En temps réel de haute fréquence avec SignalR 2
====================
par [Patrick Fletcher](https://github.com/pfletcher)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

> Ce didacticiel montre comment créer une application web qui utilise ASP.NET SignalR 2 pour fournir des fonctionnalités de messagerie haute fréquence. Messagerie de haute fréquence signifie dans ce cas de mises à jour qui sont envoyés à un taux fixe ; dans le cas de cette application, jusqu'à 10 messages par seconde.
> 
> L’application que vous allez créer dans ce didacticiel affiche une forme que les utilisateurs peuvent faire glisser. La position de la forme dans tous les autres navigateurs connectés sera ensuite être mis à jour pour correspondre à la position de la forme déplacée à l’aide de mises à jour a expiré.
> 
> Concepts présentés dans ce didacticiel ont des applications dans des jeux en temps réel et autres applications de simulation.
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

Ce didacticiel montre comment créer une application qui partage l’état d’un objet avec d’autres navigateurs en temps réel. L’application que nous allons créer est appelée MoveShape. La page MoveShape affiche un élément HTML Div que l’utilisateur peut faire glisser ; Lorsque l’utilisateur fait glisser l’élément Div, sa nouvelle position sera envoyée au serveur, ce qui indique alors tous les autres clients connectés pour mettre à jour la position de la forme pour faire correspondre.

![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

L’application créée dans ce didacticiel est basée sur une démonstration par Damian Edwards. Une vidéo contenant cette démonstration, peut être consultée [ici](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Le didacticiel démarrera en vous montrant notamment comment envoyer des messages SignalR à partir de chaque événement qui se déclenche lorsque vous faites glisser la forme. Chaque client connecté est alors à jour la position de la version locale de la forme chaque fois qu’un message est reçu.

Pendant que l’application fonctionne à l’aide de cette méthode, il n’est pas un modèle de programmation recommandé, car aucune limite supérieure au nombre de messages mise en route, afin que les clients et le serveur peuvent obtenir submergés avec des messages et seraient dégrader les performances . L’animation affichée sur le client serait également disjoint, comme la forme est déplacée instantanément par chaque méthode, plutôt que Mobile sans problème à chaque nouvel emplacement. Les sections suivantes du didacticiel va vous montrer comment créer une fonction du minuteur qui limite la vitesse maximale à laquelle les messages sont envoyés par le client ou le serveur et le déplacement de la forme sans heurts entre les emplacements. La version finale de l’application créée dans ce didacticiel peut être téléchargée à partir de [Code Gallery](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).

Ce didacticiel contient les sections suivantes :

- [Composants requis](#prerequisites)
- [Créer le projet et ajoutez le package SignalR et JQuery.UI NuGet](#createtheproject2013)
- [Créer l’application de base](#baseapp)
- [À partir du hub au démarrage de l’application](#startup2013)
- [Ajouter la boucle de client](#clientloop)
- [Ajouter la boucle de serveur](#serverloop)
- [Ajouter une animation lisse sur le client](#animation)
- [Étapes suivantes](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Prérequis

Ce didacticiel nécessite Visual Studio 2013.

<a id="createtheproject2013"></a>

## <a name="create-the-project-and-add-the-signalr-and-jqueryui-nuget-package"></a>Créer le projet et ajoutez le package SignalR et JQuery.UI NuGet

Dans cette section, nous allons créer le projet dans Visual Studio 2013.

Les étapes suivantes utilisent Visual Studio 2013 pour créer une Application de Web ASP.NET vide et ajouter les bibliothèques SignalR et jQuery.UI :

1. Dans Visual Studio, créez une Application Web ASP.NET.

    ![Créer le web](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)
2. Dans le **nouveau projet ASP.NET** fenêtre, laissez le champ **vide** sélectionné, cliquez sur **créer un projet de**.

    ![Créer le site web vide](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
3. Dans **l’Explorateur de solutions**, cliquez sur le projet, sélectionnez **ajouter | Classe de concentrateur SignalR (v2)**. Nom de la classe **MoveShapeHub.cs** et ajoutez-le au projet. Cette étape crée la **MoveShapeHub** de classe et l’ajoute au projet un ensemble de fichiers de script et les références d’assembly qui prennent en charge SignalR.

    > [!NOTE]
    > Vous pouvez également ajouter SignalR pour un projet en cliquant sur **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package** et en exécutant une commande :

    `install-package Microsoft.AspNet.SignalR`. 

    Si vous utilisez la console pour ajouter SignalR, créer la classe de concentrateur SignalR comme une étape distincte après avoir ajouté SignalR.
4. Cliquez sur **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package**. Dans la fenêtre Gestionnaire de package, exécutez la commande suivante :

    `Install-Package jQuery.UI.Combined`

    Cette commande installe la bibliothèque jQuery UI, que vous allez utiliser pour animer la forme.
5. Dans **l’Explorateur de solutions** développez le nœud Scripts. Bibliothèques de scripts pour SignalR, jQuery et jQueryUI sont visibles dans le projet.

    ![Références de bibliothèque de script](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Créer l’application de base

Dans cette section, nous allons créer une application de navigateur qui envoie l’emplacement de la forme sur le serveur lors de chaque événement de déplacement de la souris. Le serveur diffuse ensuite ces informations pour tous les autres clients connectés qu’elles sont reçues. Nous allons étendre cette application dans les sections suivantes.

1. Si vous n’avez pas déjà créé la classe MoveShapeHub.cs, dans **l’Explorateur de solutions**, avec le bouton droit sur le projet et sélectionnez **ajouter**, **classe...** . Nom de la classe **MoveShapeHub** et cliquez sur **ajouter**.
2. Remplacez le code dans la nouvelle **MoveShapeHub** classe par le code suivant.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

    La `MoveShapeHub` classe ci-dessus est une implémentation d’un concentrateur SignalR. Comme dans le [mise en route avec SignalR](tutorial-getting-started-with-signalr.md) (didacticiel), le concentrateur a une méthode qui appellent directement les clients. Dans ce cas, le client envoie un objet qui contient les nouvelles coordonnées X et Y de la forme sur le serveur, puis obtient diffusée à tous les autres clients connectés. SignalR sérialisera automatiquement cet objet à l’aide de JSON.

    L’objet qui sera envoyé au client (`ShapeModel`) contient des membres pour stocker la position de la forme. La version de l’objet sur le serveur contient également un membre pour effectuer le suivi les du client qui données sont stockées, afin que leurs données ne soit envoyé à un client donné. Ce membre utilise le `JsonIgnore` attribut pour qu’il soit sérialisé et envoyé au client.

<a id="startup2013"></a>
## <a name="starting-the-hub-when-the-application-starts"></a>À partir du hub au démarrage de l’application

1. Ensuite, nous organisons mappage vers le concentrateur lorsque l’application démarre. SignalR 2, cela en ajoutant une classe de démarrage OWIN, laquelle va appeler `MapSignalR` lors de la classe de démarrage `Configuration` méthode est exécutée au démarrage de OWIN. La classe est ajoutée au démarrage du OWIN processus à l’aide de la `OwinStartup` attribut d’assembly.

    Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Classe de démarrage OWIN**. Nom de la classe *démarrage* et cliquez sur **OK**.
2. Modifiez le contenu de Startup.cs comme suit :

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<a id="client"></a>
## <a name="adding-the-client"></a>Ajout du client

1. Ensuite, nous allons ajouter le client. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Un nouvel élément**. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez **Html Page**. Nommez la page **Default.html** et cliquez sur **ajouter**.
2. Dans **l’Explorateur de solutions**, avec le bouton droit de la page que vous venez de créer, cliquez sur **définir comme Page de démarrage**.
3. Remplacez le code par défaut dans la page HTML avec l’extrait de code suivant.

    > [!NOTE]
    > Vérifiez que le script fait référence sous correspondance les packages ajoutés à votre projet dans le dossier Scripts.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html)]

    Le code HTML et JavaScript ci-dessus crée un Div rouge forme, Active le comportement de glissement de la forme à l’aide de la bibliothèque jQuery et utilise la forme `drag` événement à la position de la forme d’envoi au serveur.
4. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Ajouter la boucle de client

Étant donné que l’envoi de l’emplacement de la forme de chaque événement mouse move créera un montant n’est pas nécessaire de trafic réseau, les messages à partir du client doivent être limitée. Nous allons utiliser le code javascript `setInterval` fonction pour configurer une boucle qui envoie des informations sur la nouvelle position sur le serveur à un taux fixe. Cette boucle est une représentation très simple d’une « jeu boucle », une fonction appelée à plusieurs reprises qui gère toutes les fonctionnalités d’un jeu ou une autre simulation.

1. Mettre à jour le code client dans la page HTML pour faire correspondre l’extrait de code suivant.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html)]

    La mise à jour ci-dessus ajoute le `updateServerModel` (fonction), qui est appelée sur une fréquence fixe. Cette fonction envoie les données de la position sur le serveur chaque fois que le `moved` indicateur indique qu’il existe de nouvelles données de position à envoyer.
2. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer. Étant donné que le nombre de messages qui sont transmis au serveur est limité, l’animation n’apparaît pas aussi simple que dans la section précédente.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Ajouter la boucle de serveur

Dans l’application actuelle, les messages envoyés à partir du serveur au client accède souvent lors de leur réception. Cela pose un problème similaire comme a été détecté sur le client ; les messages peuvent être envoyés plus souvent qu’ils sont nécessaires, et la connexion peut devenir saturation en conséquence. Cette section décrit comment mettre à jour le serveur pour implémenter un minuteur qui limite la fréquence des messages sortants.

1. Remplacez le contenu de `MoveShapeHub.cs` avec l’extrait de code suivant.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    Le code ci-dessus développe le client pour ajouter le `Broadcaster` (classe), ce qui limite la sortie des messages à l’aide de la `Timer` classe à partir de .NET framework.

    Étant donné que le hub lui-même est transitoire (il est créé chaque fois qu’il est nécessaire), le `Broadcaster` sera créé comme un singleton. L’initialisation tardive (introduite dans .NET 4) est utilisée pour différer sa création jusqu'à ce qu’il est nécessaire, garantissant que la première instance de concentrateur est complètement créée avant le démarrage de la minuterie.

    L’appel à des clients `UpdateShape` fonction est ensuite déplacée hors de concentrateur `UpdateModel` (méthode), afin qu’il n’est plus appelé immédiatement chaque fois que les messages entrants sont reçus. Au lieu de cela, les messages pour les clients seront envoyés à une fréquence de 25 appels par seconde, géré par le `_broadcastLoop` minuteur depuis la `Broadcaster` classe.

    Enfin, au lieu d’appeler la méthode du client à partir du hub directement, les `Broadcaster` classe doit obtenir une référence vers le concentrateur actuellement (`_hubContext`) à l’aide de la `GlobalHost`.
2. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer. Il y aura pas de différence visible dans le navigateur à partir de la section précédente, mais le nombre de messages qui sont transmis au client vont être activée.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Ajouter une animation lisse sur le client

L’application est presque terminée, mais nous pouvons améliorer un plus, dans le mouvement de la forme sur le client lorsqu’il est déplacé en réponse aux messages du serveur. Au lieu de la définition de la position de la forme vers le nouvel emplacement donné par le serveur, nous allons utiliser la bibliothèque JQuery UI `animate` pour déplacer la forme sans heurts entre sa position actuelle et la nouvelle fonction.

1. Mettre à jour du client `updateShape` méthode apparence telles que le code en surbrillance ci-dessous :

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

    Le code ci-dessus déplace la forme de l’ancien emplacement vers le nouveau fourni par le serveur au cours de l’intervalle d’animation (dans ce cas, il s’agit de 100 millisecondes). Toute animation précédente en cours d’exécution sur la forme est effacée avant que la nouvelle animation démarre.
2. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer. Le déplacement de la forme dans l’autre fenêtre doit s’afficher moins saccadé comme ses déplacements est interpolé sur temps au lieu d’être définie qu’une fois par message entrant.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image8.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment programmer une application SignalR qui envoie des messages de haute fréquence entre clients et serveurs. Ce modèle de communication est utile pour le développement de jeux en ligne et autres simulations, tel que [le jeu de ShootR créé avec SignalR](http://shootr.signalr.net).

L’application complète créée dans ce didacticiel peut être téléchargée à partir de [Code Gallery](https://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a).

Pour en savoir plus sur les concepts de développement SignalR, visitez les sites suivants pour le code source de SignalR et ressources :

- [Projet SignalR](http://signalr.net)
- [SignalR Github et exemples](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

Pour une procédure pas à pas sur la façon de déployer une application SignalR dans Azure, consultez [à l’aide de SignalR avec les applications Web dans Azure App Service](../deployment/using-signalr-with-azure-web-sites.md). Pour plus d’informations sur la façon de déployer un projet web Visual Studio sur un Site Web de Windows Azure, consultez [créer une application web ASP.NET dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).

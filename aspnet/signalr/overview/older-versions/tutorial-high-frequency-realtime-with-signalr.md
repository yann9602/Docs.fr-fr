---
uid: signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
title: "En temps réel de haute fréquence avec SignalR 1.x | Documents Microsoft"
author: pfletcher
description: "Ce didacticiel montre comment créer une application web qui utilise ASP.NET SignalR pour fournir des fonctionnalités de messagerie haute fréquence. Messagerie de haute fréquence..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/16/2013
ms.topic: article
ms.assetid: ad2a5da5-2e79-40ea-bc84-028d327f5982
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: 0c680a7d8b911b2734647948b683d5ff6e47aec4
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="high-frequency-realtime-with-signalr-1x"></a>En temps réel de haute fréquence avec SignalR 1.x
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Ce didacticiel montre comment créer une application web qui utilise ASP.NET SignalR pour fournir des fonctionnalités de messagerie haute fréquence. Messagerie de haute fréquence signifie dans ce cas de mises à jour qui sont envoyés à un taux fixe ; dans le cas de cette application, jusqu'à 10 messages par seconde.
> 
> L’application que vous allez créer dans ce didacticiel affiche une forme que les utilisateurs peuvent faire glisser. La position de la forme dans tous les autres navigateurs connectés sera ensuite être mis à jour pour correspondre à la position de la forme déplacée à l’aide de mises à jour a expiré.
> 
> Concepts présentés dans ce didacticiel ont des applications dans des jeux en temps réel et autres applications de simulation.
> 
> Commentaires sur le didacticiel sont les bienvenus. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com).


## <a name="overview"></a>Vue d'ensemble

Ce didacticiel montre comment créer une application qui partage l’état d’un objet avec d’autres navigateurs en temps réel. L’application que nous allons créer est appelée MoveShape. La page MoveShape affiche un élément HTML Div que l’utilisateur peut faire glisser ; Lorsque l’utilisateur fait glisser l’élément Div, sa nouvelle position sera envoyée au serveur, ce qui indique alors tous les autres clients connectés pour mettre à jour la position de la forme pour faire correspondre.

![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

L’application créée dans ce didacticiel est basée sur une démonstration par Damian Edwards. Une vidéo contenant cette démonstration, peut être consultée [ici](https://channel9.msdn.com/Series/Building-Web-Apps-with-ASP-NET-Jump-Start/Building-Web-Apps-with-ASPNET-Jump-Start-08-Real-time-Communication-with-SignalR).

Le didacticiel démarrera en vous montrant notamment comment envoyer des messages SignalR à partir de chaque événement qui se déclenche lorsque vous faites glisser la forme. Chaque client connecté est alors à jour la position de la version locale de la forme chaque fois qu’un message est reçu.

Pendant que l’application fonctionne à l’aide de cette méthode, il n’est pas un modèle de programmation recommandé, car aucune limite supérieure au nombre de messages mise en route, afin que les clients et le serveur peuvent obtenir submergés avec des messages et seraient dégrader les performances . L’animation affichée sur le client serait également disjoint, comme la forme est déplacée instantanément par chaque méthode, plutôt que Mobile sans problème à chaque nouvel emplacement. Les sections suivantes du didacticiel va vous montrer comment créer une fonction du minuteur qui limite la vitesse maximale à laquelle les messages sont envoyés par le client ou le serveur et le déplacement de la forme sans heurts entre les emplacements. La version finale de l’application créée dans ce didacticiel peut être téléchargée à partir de [Code Gallery](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Ce didacticiel contient les sections suivantes :

- [Composants requis](#prerequisites)
- [Créer le projet](#createtheproject)
- [Ajouter les packages NuGet de JQuery.UI et d’ASP.NET SignalR](#nugetpackages)
- [Créer l’application de base](#baseapp)
- [Ajouter la boucle de client](#clientloop)
- [Ajouter la boucle de serveur](#serverloop)
- [Ajouter une animation lisse sur le client](#animation)
- [Étapes suivantes](#furthersteps)

<a id="prerequisites"></a>

## <a name="prerequisites"></a>Conditions préalables

Ce didacticiel nécessite Visual Studio 2012 ou Visual Studio 2010. Si Visual Studio 2010 est utilisé, le projet utilise .NET Framework 4, plutôt que .NET Framework 4.5.

Si vous utilisez Visual Studio 2012, il est recommandé d’installer le [mise à jour ASP.NET et Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650). Cette mise à jour contient les nouvelles fonctionnalités telles que des améliorations apportées à la publication, de nouvelles fonctionnalités et nouveaux modèles.

Si vous avez Visual Studio 2010, assurez-vous que [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) est installé.

<a id="createtheproject"></a>

## <a name="create-the-project"></a>Créer le projet

Dans cette section, nous allons créer le projet dans Visual Studio.

1. À partir de la **fichier** menu, cliquez sur **nouveau projet**.
2. Dans le **nouveau projet** boîte de dialogue, développez **c#** sous **modèles** et sélectionnez **Web**.
3. Sélectionnez le **Application Web ASP.NET vide** modèle, nommez le projet *MoveShapeDemo*, puis cliquez sur **OK**.

    ![Création du nouveau projet](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-and-jqueryui-nuget-packages"></a>Ajouter les Packages NuGet de JQuery.UI SignalR

Vous pouvez ajouter des fonctionnalités de SignalR à un projet en installant un package NuGet. Ce didacticiel utilisera également le package JQuery.UI permettant la forme glisser-déposer animées.

1. Cliquez sur **outils | Gestionnaire de Package de bibliothèque | Console du Gestionnaire de package**.
2. Entrez la commande suivante dans le Gestionnaire de package.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.ps1)]

    Le package SignalR installe un nombre d’autres packages NuGet en tant que dépendances. Une fois l’installation terminée vous disposez de tous les composants serveur et client pour utiliser une application ASP.NET SignalR.
3. Entrez la commande suivante dans la console du Gestionnaire de package pour installer les packages de JQuery et JQuery.UI.

    [!code-powershell[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.ps1)]

<a id="baseapp"></a>

## <a name="create-the-base-application"></a>Créer l’application de base

Dans cette section, nous allons créer une application de navigateur qui envoie l’emplacement de la forme sur le serveur lors de chaque événement de déplacement de la souris. Le serveur diffuse ensuite ces informations pour tous les autres clients connectés qu’elles sont reçues. Nous allons étendre cette application dans les sections suivantes.

1. Dans **l’Explorateur de solutions**, avec le bouton droit sur le projet et sélectionnez **ajouter**, **classe...** . Nom de la classe **MoveShapeHub** et cliquez sur **ajouter**.
2. Remplacez le code dans la nouvelle **MoveShapeHub** classe par le code suivant.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.cs)]

    La `MoveShapeHub` classe ci-dessus est une implémentation d’un concentrateur SignalR. Comme dans le [mise en route avec SignalR](index.md) (didacticiel), le concentrateur a une méthode qui appellent directement les clients. Dans ce cas, le client envoie un objet qui contient les nouvelles coordonnées X et Y de la forme sur le serveur, puis obtient diffusée à tous les autres clients connectés. SignalR sérialisera automatiquement cet objet à l’aide de JSON.

    L’objet qui sera envoyé au client (`ShapeModel`) contient des membres pour stocker la position de la forme. La version de l’objet sur le serveur contient également un membre pour effectuer le suivi les du client qui données sont stockées, afin que leurs données ne soit envoyé à un client donné. Ce membre utilise le `JsonIgnore` attribut pour qu’il soit sérialisé et envoyé au client.
3. Ensuite, nous organisons le concentrateur lorsque l’application démarre. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Classe d’Application globale**. Acceptez le nom par défaut *Global* et cliquez sur **OK**.

    ![Ajouter la classe d’Application globale](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)
4. Ajoutez le code suivant `using` instruction après avoir fourni **à l’aide de** instructions dans la classe Global.asax.cs.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.cs)]
5. Ajoutez la ligne suivante de code dans la `Application_Start` méthode de la classe globale pour inscrire l’itinéraire par défaut pour SignalR.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

    Le fichier global.asax doit se présenter comme suit :

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.cs)]
6. Ensuite, nous allons ajouter le client. Dans **l’Explorateur de solutions**, cliquez sur le projet, puis cliquez sur **ajouter | Un nouvel élément**. Dans le **ajouter un nouvel élément** boîte de dialogue, sélectionnez **Html Page**. Donner un nom approprié à la page (comme **Default.html**) et cliquez sur **ajouter**.
7. Dans **l’Explorateur de solutions**, avec le bouton droit de la page que vous venez de créer, cliquez sur **définir comme Page de démarrage**.
8. Remplacez le code par défaut dans la page HTML avec l’extrait de code suivant.

    > [!NOTE]
    > Vérifiez que le script fait référence sous correspondance les packages ajoutés à votre projet dans le dossier Scripts. Dans Visual Studio 2010, la version de JQuery et SignalR ajouté au projet que correspondent pas les numéros de version ci-dessous.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample7.html)]

    Le code HTML et JavaScript ci-dessus crée un Div rouge forme, Active le comportement de glissement de la forme à l’aide de la bibliothèque jQuery et utilise la forme `drag` événement à la position de la forme d’envoi au serveur.
9. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image4.png)

<a id="clientloop"></a>

## <a name="add-the-client-loop"></a>Ajouter la boucle de client

Étant donné que l’envoi de l’emplacement de la forme de chaque événement mouse move créera un montant n’est pas nécessaire de trafic réseau, les messages à partir du client doivent être limitée. Nous allons utiliser le code javascript `setInterval` fonction pour configurer une boucle qui envoie des informations sur la nouvelle position sur le serveur à un taux fixe. Cette boucle est une représentation très simple d’une « jeu boucle », une fonction appelée à plusieurs reprises qui gère toutes les fonctionnalités d’un jeu ou une autre simulation.

1. Mettre à jour le code client dans la page HTML pour faire correspondre l’extrait de code suivant.

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample8.html)]

    La mise à jour ci-dessus ajoute le `updateServerModel` (fonction), qui est appelée sur une fréquence fixe. Cette fonction envoie les données de la position sur le serveur chaque fois que le `moved` indicateur indique qu’il existe de nouvelles données de position à envoyer.
2. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer. Étant donné que le nombre de messages qui sont transmis au serveur est limité, l’animation n’apparaît pas aussi simple que dans la section précédente.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image5.png)

<a id="serverloop"></a>

## <a name="add-the-server-loop"></a>Ajouter la boucle de serveur

Dans l’application actuelle, les messages envoyés à partir du serveur au client accède souvent lors de leur réception. Cela pose un problème similaire comme a été détecté sur le client ; les messages peuvent être envoyés plus souvent qu’ils sont nécessaires, et la connexion peut devenir saturation en conséquence. Cette section décrit comment mettre à jour le serveur pour implémenter un minuteur qui limite la fréquence des messages sortants.

1. Remplacez le contenu de `MoveShapeHub.cs` avec l’extrait de code suivant.

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample9.cs)]

    Le code ci-dessus développe le client pour ajouter le `Broadcaster` (classe), ce qui limite la sortie des messages à l’aide de la `Timer` classe à partir de .NET framework.

    Étant donné que le hub lui-même est transitoire (il est créé chaque fois qu’il est nécessaire), le `Broadcaster` sera créé comme un singleton. L’initialisation tardive (introduite dans .NET 4) est utilisée pour différer sa création jusqu'à ce qu’il est nécessaire, garantissant que la première instance de concentrateur est complètement créée avant le démarrage de la minuterie.

    L’appel à des clients `UpdateShape` fonction est ensuite déplacée hors de concentrateur `UpdateModel` (méthode), afin qu’il n’est plus appelé immédiatement chaque fois que les messages entrants sont reçus. Au lieu de cela, les messages pour les clients seront envoyés à une fréquence de 25 appels par seconde, géré par le `_broadcastLoop` minuteur depuis la `Broadcaster` classe.

    Enfin, au lieu d’appeler la méthode du client à partir du hub directement, les `Broadcaster` classe doit obtenir une référence vers le concentrateur actuellement (`_hubContext`) à l’aide de la `GlobalHost`.
2. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer. Il y aura pas de différence visible dans le navigateur à partir de la section précédente, mais le nombre de messages qui sont transmis au client vont être activée.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image6.png)

<a id="animation"></a>

## <a name="add-smooth-animation-on-the-client"></a>Ajouter une animation lisse sur le client

L’application est presque terminée, mais nous pouvons améliorer un plus, dans le mouvement de la forme sur le client lorsqu’il est déplacé en réponse aux messages du serveur. Au lieu de la définition de la position de la forme vers le nouvel emplacement donné par le serveur, nous allons utiliser la bibliothèque JQuery UI `animate` pour déplacer la forme sans heurts entre sa position actuelle et la nouvelle fonction.

1. Mettre à jour du client `updateShape` méthode apparence telles que le code en surbrillance ci-dessous :

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample10.html?highlight=35-42)]

    Le code ci-dessus déplace la forme de l’ancien emplacement vers le nouveau fourni par le serveur au cours de l’intervalle d’animation (dans ce cas, il s’agit de 100 millisecondes). Toute animation précédente en cours d’exécution sur la forme est effacée avant que la nouvelle animation démarre.
2. Démarrez l’application en appuyant sur F5. Copier l’URL de la page et les coller dans une deuxième fenêtre de navigateur. Faites glisser la forme dans une fenêtre du navigateur ; la forme dans l’autre fenêtre de navigateur doit déplacer. Le déplacement de la forme dans l’autre fenêtre doit s’afficher moins saccadé comme ses déplacements est interpolé sur temps au lieu d’être définie qu’une fois par message entrant.

    ![La fenêtre d’application](tutorial-high-frequency-realtime-with-signalr/_static/image7.png)

<a id="furthersteps"></a>

## <a name="further-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment programmer une application SignalR qui envoie des messages de haute fréquence entre clients et serveurs. Ce modèle de communication est utile pour le développement de jeux en ligne et autres simulations, tel que [le jeu de ShootR créé avec SignalR](http://shootr.signalr.net).

L’application complète créée dans ce didacticiel peut être téléchargée à partir de [Code Gallery](https://code.msdn.microsoft.com/SignalR-MoveShape-demo-3366dac6).

Pour en savoir plus sur les concepts de développement SignalR, visitez les sites suivants pour le code source de SignalR et ressources :

- [Projet SignalR](http://signalr.net)
- [SignalR Github et exemples](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

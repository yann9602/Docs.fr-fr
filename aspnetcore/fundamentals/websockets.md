---
title: Prise en charge de WebSocket dans ASP.NET Core
author: tdykstra
description: "Quel est le protocole WebSocket prennent en charge ASP.NET Core et comment l’utiliser."
keywords: ASP.NET Core, WebSockets
ms.author: tdykstra
manager: wpickett
ms.date: 03/25/2017
ms.topic: article
ms.assetid: 0e0fedcd-a7b4-4479-8ae0-36eab0229d7e
ms.technology: aspnet
ms.prod: aspnet-core
uid: fundamentals/websockets
ms.openlocfilehash: 8a6b5cc8ca8ac17f0e4c5b23f20013130cd472c8
ms.sourcegitcommit: 78d28178345a0eea91556e4cd1adad98b1446db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2017
---
# <a name="introduction-to-websockets-in-aspnet-core"></a>Introduction à WebSockets dans ASP.NET Core

Par [Tom Dykstra](https://github.com/tdykstra) et [Andrew Stanton-infirmière](https://github.com/anurse)

Cet article explique comment démarrer avec le protocole WebSocket dans ASP.NET Core. [WebSocket](https://wikipedia.org/wiki/WebSocket) est un protocole qui permet des canaux de communication persistant bidirectionnelle sur les connexions TCP. Il est utilisé pour les applications telles que la conversation, téléscripteurs, jeux, n’importe où vous souhaitez les fonctionnalités en temps réel dans une application web.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample). Consultez le [étapes](#next-steps) section pour plus d’informations.


## <a name="prerequisites"></a>Conditions préalables

* ASP.NET Core 1.1 (ne s’exécute pas sur la version 1.0)
* Un système d’exploitation ASP.NET Core s’exécute sur :
  
  * Windows 7 / Windows Server 2008 et versions ultérieur
  * Linux
  * MacOS

* **Exception**: Si votre application s’exécute sur Windows avec IIS, ou avec WebListener, vous devez utiliser :

  * Windows 8 / Windows Server 2012 ou version ultérieure
  * IIS 8 / IIS 8 Express
  * WebSocket doit être activé dans IIS

* Pour les navigateurs pris en charge, consultez http://caniuse.com/#feat=websockets.

## <a name="when-to-use-it"></a>Quand l'utiliser

Utiliser des WebSockets lorsque vous avez besoin travailler directement avec une connexion de socket. Par exemple, vous devrez peut-être les meilleures performances possibles pour un jeu en temps réel.

[ASP.NET SignalR](https://docs.microsoft.com/aspnet/signalr/overview/getting-started/introduction-to-signalr) fournit un riche modèle d’application de la fonctionnalité en temps réel, mais il s’exécute uniquement sur ASP.NET, pas ASP.NET Core. Une version de base de SignalR est en cours de développement ; pour suivre sa progression, consultez le [référentiel GitHub pour SignalR Core](https://github.com/aspnet/SignalR).

Si vous ne souhaitez pas attendre que SignalR Core, vous pouvez utiliser des WebSockets directement maintenant. Toutefois, vous devrez peut-être développer les fonctionnalités SignalR fournirait, telles que :

* Prise en charge pour un plus grand nombre de versions de navigateur à l’aide de secours automatique de méthodes de transport secondaire.
* Reconnexion automatique lors de la connexion.
* Prise en charge pour les clients de l’appel de méthodes sur le serveur, ou vice versa.
* Prise en charge pour la mise à l’échelle sur plusieurs serveurs.

## <a name="how-to-use-it"></a>Comment l’utiliser

* Installer le [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.
* Configurer l’intergiciel (middleware).
* Accepter les demandes WebSocket.
* Envoyer et recevoir des messages.

### <a name="configure-the-middleware"></a>Configurer l’intergiciel (middleware)

Ajouter l’intergiciel (middleware) WebSockets dans le `Configure` méthode de la `Startup` classe.

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSockets)]

Les paramètres suivants peuvent être configurés :

* `KeepAliveInterval`-La fréquence à laquelle envoyer des trames de « ping » pour le client, pour vous assurer de proxys maintiennent ouverte la connexion.
* `ReceiveBufferSize`-La taille de la mémoire tampon utilisée pour recevoir des données. Seuls les utilisateurs expérimentés devrez modifier ce paramètre, pour le réglage des performances basée sur la taille de leurs données.

[!code-csharp[](websockets/sample/Startup.cs?name=UseWebSocketsOptions)]

### <a name="accept-websocket-requests"></a>Accepter les demandes de WebSocket

Plus tard dans le cycle de vie de demande (plus loin dans le `Configure` méthode ou dans une action MVC, par exemple) vérifier s’il est une demande WebSocket et accepter la demande WebSocket.

Cet exemple provient plus tard dans le `Configure` (méthode).

[!code-csharp[](websockets/sample/Startup.cs?name=AcceptWebSocket&highlight=7)]

Une demande WebSocket peut provenir sur n’importe quelle URL, mais cet exemple de code n’accepte que les demandes de `/ws`.

### <a name="send-and-receive-messages"></a>Envoyer et recevoir des messages

Le `AcceptWebSocketAsync` méthode met à niveau de la connexion TCP pour une connexion WebSocket et vous donne un [WebSocket](https://docs.microsoft.com/dotnet/core/api/system.net.websockets.websocket) objet. Utilisez l’objet WebSocket pour envoyer et recevoir des messages.

Le code illustré précédemment et qui accepte la demande WebSocket passe le `WebSocket` de l’objet à un `Echo` méthode ; c' est ici le `Echo` (méthode). Le code reçoit un message et envoie immédiatement le même message. Il reste dans une boucle cela jusqu'à ce que le client ferme la connexion. 

[!code-csharp[](websockets/sample/Startup.cs?name=Echo)]

Lorsque vous acceptez le WebSocket avant de commencer cette boucle, le pipeline de l’intergiciel (middleware) se termine.  La fermeture du socket, le pipeline se déroule. Autrement dit, l’arrêt de demande de déplacement vers l’avant dans le pipeline lorsque vous acceptez un WebSocket, tel qu’il serait lorsque vous atteignez une action MVC, par exemple.  Mais lorsque vous terminez cette boucle et que vous fermez le socket, la demande se poursuit sauvegarder le pipeline.

## <a name="next-steps"></a>Étapes suivantes

Le [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/websockets/sample) qui accompagne cette article est une application simple écho. Il a une page web qui établit des connexions de WebSocket, et le serveur renvoie simplement au client les messages qu’il reçoit. Exécuter à partir d’une invite de commandes (il n'a pas configuré pour exécuter à partir de Visual Studio avec IIS Express) et accédez à http://localhost : 5000. La page web affiche l’état de la connexion en haut à gauche :

![État initial de la page web](websockets/_static/start.png)

Sélectionnez **connexion** pour envoyer une demande WebSocket à l’URL affichée.  Entrez un message de test et sélectionnez **envoyer**. Lorsque vous avez terminé, sélectionnez **Socket fermer**. Le **Communication journal** section signale chaque ouverture, d’envoi et l’action Fermer tel qu’il se produit.

![État initial de la page web](websockets/_static/end.png)

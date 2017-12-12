---
uid: signalr/overview/getting-started/introduction-to-signalr
title: "Introduction à SignalR | Documents Microsoft"
author: pfletcher
description: "Cet article décrit les nouveautés du SignalR et certaines solutions qu’il a été conçu pour créer."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: 0fab5e35-8c1f-43d4-8635-b8aba8766a71
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/getting-started/introduction-to-signalr
msc.type: authoredcontent
ms.openlocfilehash: 27150d314b6861f1098e6ef4a7de94e7b371a78e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="introduction-to-signalr"></a>Introduction à SignalR
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Cet article décrit les nouveautés du SignalR et certaines solutions qu’il a été conçu pour créer. 
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="what-is-signalr"></a>Nouveautés SignalR

ASP.NET SignalR est une bibliothèque pour les développeurs ASP.NET qui simplifie le processus d’ajout de fonctionnalités web en temps réel aux applications. Les fonctionnalités web en temps réel sont la possibilité que de push de code serveur contenu aux clients connectés instantanément dès que possible, au lieu d’utiliser le serveur d’attendre un client demander de nouvelles données.

SignalR peut servir à ajouter de fonctionnalités web « en temps réel » quelconque à votre application ASP.NET. Lors de la conversation est souvent utilisée comme un exemple, vous pouvez effectuer beaucoup plus. Chaque fois qu’un utilisateur actualise une page web pour afficher les nouvelles données, ou la page implémente [interrogation longue](http://en.wikipedia.org/wiki/Push_technology#Long_polling) pour extraire les nouvelles données, il constitue un candidat pour l’utilisation de SignalR. Exemples de tableaux de bord et de l’analyse des applications, des applications de collaboration (par exemple, l’Édition simultanée de documents), la fonction mises à jour de progression et des formulaires en temps réel.

SignalR permet également complètement nouveaux types d’applications web qui requièrent de haute fréquence des mises à jour à partir du serveur, par exemple, les jeux en temps réel. Pour obtenir un bon exemple de cela, consultez le [ShootR jeu.](http://shootr.signalr.net/)

SignalR fournit une API simple pour la création d’appels de procédure distante du serveur-client (RPC) qui appellent les fonctions JavaScript dans le client (et autres plateformes de client) à partir du code .NET côté serveur. SignalR inclut également des API pour la gestion des connexions (par exemple, vous connecter et événements de déconnexion) et le regroupement de connexions.

![Appeler des méthodes avec SignalR](introduction-to-signalr/_static/image1.png)

SignalR gère automatiquement la gestion des connexions et vous permet de messages de diffusion à tous les clients connectés simultanément, comme une salle de conversation. Vous pouvez également envoyer des messages à des clients spécifiques. La connexion entre le client et le serveur est persistante, contrairement à une connexion HTTP classique, ce qui a été établie pour chaque communication.

SignalR prend en charge la fonctionnalité « server push », dans lequel le code de serveur peut faire appel à code de client dans le navigateur en utilisant les appels de procédure distante (RPC), plutôt que le modèle requête-réponse sur le web aujourd'hui.

Les applications SignalR peuvent évoluer à des milliers de clients à l’aide du Bus des services, SQL Server ou [Redis](http://redis.io).

SignalR est open source, accessible via [GitHub](https://github.com/signalr).

## <a name="signalr-and-websocket"></a>SignalR et WebSocket

SignalR utilise le nouveau transport WebSocket lorsqu’elles sont disponibles et revient aux transports antérieures en cas de besoin. Vous pouvez certainement écrire votre application à l’aide de WebSocket directement, à l’aide des moyens SignalR un grand nombre de fonctionnalités supplémentaires, que vous devez implémenter est déjà effectuées pour vous. Plus important encore, cela signifie que vous pouvez programmer votre application pour tirer parti de WebSocket sans avoir à vous soucier de la création d’un chemin d’accès du code distinct pour les clients plus anciens. SignalR protège également de ne pas avoir à vous soucier des mises à jour de WebSocket, étant donné que SignalR continuera à être mis à jour pour prendre en charge les modifications dans le transport sous-jacent, fournissant une interface cohérente entre les versions de WebSocket à votre application.

Pendant que vous pouvez certainement créer une solution à l’aide de WebSocket uniquement, SignalR fournit toutes les fonctionnalités que vous devez écrire vous-même, telles que de secours pour les autres transports et de révision de votre application des mises à jour pour les implémentations de WebSocket.

<a id="transports"></a>

## <a name="transports-and-fallbacks"></a>Transports et secours

SignalR est une abstraction sur certains des transports qui sont nécessaires pour effectuer un travail en temps réel entre client et serveur. Une connexion SignalR démarre en tant que HTTP et est ensuite promue pour une connexion WebSocket s’il est disponible. WebSocket est le transport idéal pour SignalR, car elle permet d’optimiser l’utilisation de mémoire du serveur a la latence la plus faible et possède les fonctionnalités les plus sous-jacent (par exemple, la communication bidirectionnelle entre client et serveur), mais il comporte également les plus strictes configuration requise : WebSocket nécessite le serveur pour être à l’aide de .NET Framework 4.5 ou Windows 8 et Windows Server 2012. Si ces conditions ne sont pas remplies, SignalR va tenter d’utiliser d’autres transports ses connexions.

### <a name="html-5-transports"></a>Les transports de HTML 5

Ces transports dépendent de la prise en charge de [HTML 5](http://en.wikipedia.org/wiki/HTML5). Si le navigateur client ne prend pas en charge la norme HTML 5, transports antérieures seront utilisés.

- **WebSocket** (si le le serveur et le navigateur indiquent qu’ils peuvent prendre en charge de Websocket). WebSocket est le seul transport qui établit une connexion permanente, bidirectionnelle true entre client et serveur. Toutefois, WebSocket a également les exigences plus strictes ; Il est entièrement prise en charge uniquement dans les dernières versions de Microsoft Internet Explorer et Google Chrome, Mozilla Firefox et n’a qu’une implémentation partielle dans d’autres navigateurs, telles que Opera et Safari.
- **Événements envoyés du serveur**, également appelé EventSource (si le navigateur prend en charge serveur envoyées événements, qui est essentiellement tous les navigateurs, à l’exception d’Internet Explorer.)

### <a name="comet-transports"></a>Transports Comet

Les transports suivants sont basés sur le [Comet](http://en.wikipedia.org/wiki/Comet_(programming)) modèle d’application web, dans laquelle un navigateur ou un autre client maintient une demande HTTP maintenu de longue durée, le serveur peut utiliser pour transmettre des données au client sans le client en particulier il demande.

- **Forever Frame** (pour Internet Explorer uniquement). Forever Frame crée un IFrame masqué qui effectue une demande à un point de terminaison sur le serveur qui ne se termine pas. Le serveur envoie ensuite en permanence script vers le client qui est exécuté immédiatement, en fournissant une connexion en temps réel à sens unique à partir du serveur au client. La connexion client au serveur utilise une connexion distincte à partir du serveur pour la connexion du client, et comme une requête HTTP standard, une nouvelle connexion est créée pour chaque élément de données qui doit être envoyé.
- **AJAX longue d’interrogation**. L’interrogation longue ne crée pas une connexion permanente, mais au lieu de cela interroge le serveur avec une demande qui reste ouverte jusqu'à ce que le serveur répond, à partir de laquelle la connexion se ferme et une nouvelle connexion est demandée immédiatement. Cela peut générer une certaine latence lors de la connexion réinitialise.

Pour plus d’informations sur les transports sont pris en charge dans les configurations, consultez [plateformes prises en charge](supported-platforms.md).

### <a name="transport-selection-process"></a>Processus de sélection de transport

La liste suivante décrit les étapes que SignalR utilise pour déterminer le transport à utiliser.

1. Si le navigateur Internet Explorer 8 ou une version antérieure, l’interrogation longue est utilisé.
2. Si JSONP est configuré (autrement dit, le `jsonp` paramètre est défini sur `true` au démarrage de la connexion), interrogation longue est utilisé.
3. Si une connexion entre domaines est effectuée (autrement dit, si le point de terminaison SignalR ne se trouve pas dans le même domaine que la page d’hébergement), WebSocket est utilisé si les critères suivants sont satisfaits :

    - Le client prend en charge CORS (partage des ressources Cross-Origin). Pour plus d’informations sur lequel les clients prennent en charge CORS, consultez [CORS au caniuse.com](http://www.caniuse.com/CORS).
    - Le client prend en charge de WebSocket
    - Le serveur prend en charge de WebSocket

    Si un de ces critères ne sont pas remplie, interrogation longue sera utilisé. Pour plus d’informations sur les connexions entre domaines, consultez [comment établir une connexion entre domaines](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).
4. Si JSONP n’est pas configuré et que la connexion n’est pas entre domaines, WebSocket est utilisé à la fois le client et le serveur de prise en charge.
5. Si le client ou le serveur ne prennent pas en charge WebSocket, les événements envoyés du serveur est utilisé s’il est disponible.
6. Si les événements envoyés du serveur n’est pas disponible, Forever Frame est tentée.
7. Si Forever Frame échoue, l’interrogation longue est utilisé.

<a id="MonitoringTransports"></a>
### <a name="monitoring-transports"></a>Surveillance des transports

Vous pouvez déterminer que le transport à l’aide de votre application en activant la journalisation sur votre concentrateur et en ouvrant la fenêtre de console dans votre navigateur.

Pour activer la journalisation des événements de votre concentrateur dans un navigateur, ajoutez la commande suivante à votre application cliente :

`$.connection.hub.logging = true;`

- Dans Internet Explorer, ouvrez les outils de développement en appuyant sur F12 et cliquez sur l’onglet de la Console.

    ![Console dans Microsoft Internet Explorer](introduction-to-signalr/_static/image2.png)
- Dans Chrome, ouvrez la console en appuyant sur Ctrl + Maj + J.

    ![Console Google Chrome](introduction-to-signalr/_static/image3.png)

Avec l’ouverture de la console et la journalisation activée, vous serez en mesure de vérifier que le transport est utilisé par SignalR.

![Dans Internet Explorer affichant le transport WebSocket de la console](introduction-to-signalr/_static/image4.png)

### <a name="specifying-a-transport"></a>Spécification d’un transport

Négocier un transport prend un certain temps et de client/serveur ressources. Si les fonctionnalités de client sont connues, puis un transport peut être spécifié lors de la connexion client est démarrée. L’extrait de code suivant illustre le démarrage d’une connexion à l’aide du transport d’interrogation longue Ajax, que vous utiliseriez si il était connu que le client ne prenait pas en charge un autre protocole :

`connection.start({ transport: 'longPolling' });`

Vous pouvez spécifier un ordre de secours si vous souhaitez un client tente de transports spécifiques dans l’ordre. L’extrait de code suivant illustre le WebSocket lors de la tentative et cas d’échec, passer directement à l’interrogation longue.

`connection.start({ transport: ['webSockets','longPolling'] });`

Les constantes de chaîne pour la spécification des transports sont définies comme suit :

- `webSockets`
- `foreverFrame`
- `serverSentEvents`
- `longPolling`

## <a name="connections-and-hubs"></a>Les connexions et les concentrateurs

L’API SignalR contient deux modèles pour la communication entre les clients et serveurs : connexions persistantes et les concentrateurs.

Une connexion représente un point de terminaison simple pour l’envoi de messages unique destinataire, regroupées ou de diffusion. La donne les API de connexion persistantes (représenté par la classe persistentconnection ne dans le code .NET) au développeur un accès direct au protocole de communication de bas niveau qui expose de SignalR. À l’aide du modèle de communication de connexions seront familière aux développeurs qui ont utilisé l’API de connexion telles que Windows Communication Foundation.

Un concentrateur est un pipeline de plus haut niveau basé sur l’API de connexion qui permet à votre client et le serveur appeler des méthodes sur eux directement. SignalR gère la répartition entre les limites de l’ordinateur, comme par magie, ce qui permet aux clients d’appeler des méthodes sur le serveur en tant que facilement en tant que méthodes locales et vice versa. L’utilisation du modèle de communication concentrateurs seront familière aux développeurs qui ont utilisé l’appel à distance les API telles que la communication à distance .NET. À l’aide d’un concentrateur vous permet également de passer des paramètres fortement typés aux méthodes, l’activation de liaison de modèle.

### <a name="architecture-diagram"></a>diagramme d’architecture

Le diagramme suivant montre la relation entre les concentrateurs, des connexions persistantes et les technologies sous-jacentes utilisées pour les transports.

![Diagramme d’Architecture SignalR montrant les API, les transports et les clients](introduction-to-signalr/_static/image5.png)

### <a name="how-hubs-work"></a>Comment fonctionnent les concentrateurs

Lorsque le code côté serveur appelle une méthode sur le client, un paquet est envoyé sur le transport actif qui contient le nom et les paramètres de la méthode à appeler (lorsqu’un objet est envoyé en tant que paramètre de méthode, il est sérialisé à l’aide de JSON). Le client fait ensuite correspond le nom de méthode pour les méthodes définies dans le code côté client. S’il existe une correspondance, la méthode du client sera exécutée en utilisant les données de paramètre désérialisé.

L’appel de méthode peut être surveillé à l’aide des outils tels que [Fiddler.](http://fiddler2.com/) L’illustration suivante montre un appel de méthode envoyé à partir d’un serveur SignalR pour un client de navigateur web dans le volet des journaux de Fiddler. L’appel de méthode est envoyé à partir d’un concentrateur appelé `MoveShapeHub`, et la méthode appelée est appelée `updateShape`.

![Affichage du journal de Fiddler montrant le trafic de SignalR](introduction-to-signalr/_static/image6.png)

Dans cet exemple, le nom du concentrateur est identifié par le `H` paramètre ; la méthode nom est identifié avec le `M` paramètre et les données envoyées à la méthode est identifiée par le `A` paramètre. L’application qui a généré ce message est créée dans le [en temps réel de haute fréquence](tutorial-high-frequency-realtime-with-signalr.md) didacticiel.

### <a name="choosing-a-communication-model"></a>Choix d’un modèle de communication

La plupart des applications doivent utiliser l’API de concentrateurs. L’API de connexions peut être utilisée dans les circonstances suivantes :

- Le format des besoins réel des messages envoyés à être spécifié.
- Le développeur souhaite travailler avec un modèle de messagerie et de distribution plutôt que d’un modèle d’appel à distance.
- Une application existante qui utilise un modèle de messagerie est portée pour utiliser SignalR.

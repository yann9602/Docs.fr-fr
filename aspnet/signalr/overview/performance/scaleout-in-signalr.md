---
uid: signalr/overview/performance/scaleout-in-signalr
title: "Introduction à la montée en puissance parallèle dans SignalR | Documents Microsoft"
author: MikeWasson
description: "Versions du logiciel utilisé dans cette rubrique Visual Studio 2013 .NET 4.5 SignalR les versions précédentes de la version 2 de cette rubrique pour plus d’informations sur les versions antérieures de..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: 7e781fc1-1c1f-45a8-bc1d-338e96dbe9c9
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/performance/scaleout-in-signalr
msc.type: authoredcontent
ms.openlocfilehash: f1d15250682305f6d0512b72bd2e40cb4a8a18e5
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="introduction-to-scaleout-in-signalr"></a>Introduction à la montée en puissance parallèle dans SignalR
====================
par [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)

> ## <a name="software-versions-used-in-this-topic"></a>Versions du logiciel utilisées dans cette rubrique
> 
> 
> - [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads)
> - .NET 4.5
> - SignalR version 2
>   
> 
> 
> ## <a name="previous-versions-of-this-topic"></a>Versions précédentes de cette rubrique
> 
> Pour plus d’informations sur les versions antérieures de SignalR, consultez [SignalR des Versions antérieures](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


En règle générale, il existe des deux façons de mettre à l’échelle d’une application web : *montée en puissance* et *montée en puissance parallèle*.

- Montée en puissance signifie l’utilisation d’un plus grand serveur (ou une machine virtuelle supérieure) avec plus de RAM, UC, etc.
- Signifie que l’ajout de plusieurs serveurs pour gérer la charge à grande échelle.

Le problème avec l’évolution verticale est que vous atteignez rapidement une limite de la taille de l’ordinateur. En outre, vous devez monter en charge. Toutefois, lorsque vous monter en charge, les clients peuvent obtenir acheminés vers différents serveurs. Un client qui est connecté à un serveur ne recevra pas les messages envoyés à partir d’un autre serveur.

![](scaleout-in-signalr/_static/image1.png)

Une solution consiste à transférer des messages entre serveurs, à l’aide d’un composant appelé un *fond de panier*. Avec un fond de panier activé, chaque instance de l’application envoie des messages au fond de panier, et le fond de panier les transfère vers les autres instances de l’application. (En électronique, un fond de panier est un groupe de connecteurs parallèles. Par analogie, un fond de panier SignalR connecte plusieurs serveurs.)

![](scaleout-in-signalr/_static/image2.png)

SignalR fournit actuellement les fonds de panier de trois :

- **Azure Service Bus**. Service Bus est une infrastructure de messagerie qui permet aux composants d’envoyer des messages d’une manière faiblement couplée.
- **Redis**. Redis est un magasin clé-valeur de mémoire. Redis prend en charge un modèle de publication/abonnement (« pub/sub ») pour envoyer des messages.
- **SQL Server**. L’infrastructure d’intégration SQL Server écrit des messages dans des tables SQL. L’infrastructure d’intégration utilise Service Broker de messagerie efficace. Toutefois, il fonctionne également si Service Broker n’est pas activé.

Si vous déployez votre application sur Azure, envisagez d’utiliser le fond de panier de Redis à l’aide de [Cache Redis Azure](https://azure.microsoft.com/services/cache/). Si vous déployez sur votre propre batterie de serveurs, envisagez de SQL Server ou les fonds de panier Redis.

Les rubriques suivantes contiennent des didacticiels pas à pas pour chaque fond de panier :

- [Montée en puissance parallèle de SignalR avec Azure Service Bus](scaleout-with-windows-azure-service-bus.md)
- [Montée en puissance parallèle de SignalR avec Redis](scaleout-with-redis.md)
- [Montée en puissance parallèle de SignalR avec SQL Server](scaleout-with-sql-server.md)

## <a name="implementation"></a>Implémentation

Dans SignalR, chaque message est envoyé via un bus de messages. Un bus de messages implémente la [IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) interface, qui fournit une abstraction publication/abonnement. Les fonds de panier de travail en remplaçant la valeur par défaut **IMessageBus** avec un bus conçu pour que fond de panier. Par exemple, le bus de messages pour Redis est [RedisMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.redis.redismessagebus(v=vs.100).aspx), et il utilise le Redis [pub/sub](http://redis.io/topics/pubsub) mécanisme pour envoyer et recevoir des messages.

Chaque instance de serveur se connecte au fond de panier via le bus. Lorsqu’un message est envoyé, il est placé dans le fond de panier, et le fond de panier envoie à tous les serveurs. Lorsqu’un serveur reçoit un message à partir de l’infrastructure d’intégration, il place le message dans sa mémoire cache locale. Le serveur puis remet les messages aux clients à partir de sa mémoire cache locale.

Pour chaque connexion client, progression du client dans la lecture du flux de message suivi est effectuée à l’aide d’un curseur. (Un curseur représente une position dans le flux de message.) Si un client se déconnecte, puis se reconnecte, il demande le bus de messages reçus après la valeur du curseur du client. La même chose se produit lorsqu’une connexion utilise [interrogation longue](../getting-started/introduction-to-signalr.md#transports). Après l’achèvement d’une requête d’interrogation longue, le client ouvre une nouvelle connexion et la demande pour les messages reçus après le curseur.

Le fonctionnement du mécanisme curseur même si un client est routé vers un autre serveur de se reconnecter. Le fond de panier tient compte de tous les serveurs, et peu importe le serveur auquel le client se connecte.

## <a name="limitations"></a>Limitations

À l’aide de fond de panier, le débit maximum de messages est plus faible que lorsque les clients communiquent directement avec un nœud de serveur unique. C’est parce que le fond de panier transfère tous les messages pour tous les nœuds, donc le fond de panier peut devenir un goulot d’étranglement. Si cette limitation est un problème dépend de l’application. Par exemple, voici quelques scénarios classiques de SignalR :

- [Serveur diffusion](../getting-started/tutorial-server-broadcast-with-signalr.md) (par exemple, les cotations boursières) : fonds de panier fonctionnent bien pour ce scénario, étant donné que le serveur contrôle la vitesse à laquelle les messages sont envoyés.
- [Client à](../getting-started/tutorial-getting-started-with-signalr.md) (par exemple, chat) : dans ce scénario, l’infrastructure d’intégration peut être un goulot d’étranglement si le nombre de messages augmentent avec le nombre de clients ; autrement dit, si le taux de messages augmente proportionnellement plus les clients se connecter.
- [En temps réel de haute fréquence](../getting-started/tutorial-high-frequency-realtime-with-signalr.md) (par exemple, les jeux en temps réel) : un fond de panier n’est pas recommandée pour ce scénario.

## <a name="enabling-tracing-for-signalr-scaleout"></a>Activation du suivi pour la montée en puissance parallèle SignalR

Pour activer le traçage pour les fonds de panier, ajoutez les sections suivantes dans le fichier web.config, sous la racine **configuration** élément :

[!code-html[Main](scaleout-in-signalr/samples/sample1.html)]

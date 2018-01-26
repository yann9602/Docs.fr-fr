---
uid: signalr/overview/older-versions/signalr-performance
title: SignalR performances (SignalR 1.x) | Documents Microsoft
author: pfletcher
description: Performances de SignalR
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/03/2013
ms.topic: article
ms.assetid: 9594d644-66b6-4223-acdd-23e29a6e4c46
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/signalr-performance
msc.type: authoredcontent
ms.openlocfilehash: bad742af28d6c36bb1b66207c2ba09d140332449
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="signalr-performance-signalr-1x"></a>SignalR performances (SignalR 1.x)
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Cette rubrique décrit comment concevoir pour, mesurer et améliorer les performances dans une application SignalR.


Pour une présentation récente sur SignalR performances et mise à l’échelle, consultez [mise à l’échelle Web en temps réel avec ASP.NET SignalR](https://channel9.msdn.com/Events/Build/2013/3-502).

Cette rubrique contient les sections suivantes :

- [Considérations de conception](#design)
- [Réglage de votre serveur SignalR pour des performances](#tuning)
- [Résolution des problèmes de performances](#troubleshooting)
- [À l’aide des compteurs de performance SignalR](#perfcounters)
- [À l’aide d’autres compteurs de performances](#othercounters)
- [Autres ressources](#otherresources)

<a id="design"></a>

## <a name="design-considerations"></a>Considérations de conception

Cette section décrit les modèles qui peuvent être implémentées pendant la conception d’une application SignalR, pour vous assurer que les performances ne soit pas entravé en générant le trafic réseau inutile.

### <a name="throttling-message-frequency"></a>Limitation de la fréquence des messages

Même dans une application qui envoie des messages à une fréquence élevée (par exemple, une application de jeu en temps réel), la plupart des applications n’avez pas besoin d’envoyer plusieurs messages par seconde. Pour réduire la quantité de trafic qui génère de chaque client, une boucle de message peut être implémentée que les files d’attente et envoie des messages ne dépasse pas fréquemment un taux fixe (autrement dit, un certain nombre de messages est envoyée par seconde, s’il existe des messages pendant cette période dans terval envoi). Pour un exemple d’application qui limite les messages à un certain taux (à partir de client et serveur), consultez [en temps réel de haute fréquence avec SignalR](../getting-started/tutorial-high-frequency-realtime-with-signalr.md).

### <a name="reducing-message-size"></a>La réduction de taille de message

Vous pouvez réduire la taille d’un message SignalR en réduisant la taille de vos objets sérialisés. Dans le code serveur, si vous envoyez un objet qui contient les propriétés que vous n’avez pas besoin d’être transmis, empêcher ces propriétés en cours de sérialisation à l’aide de la `JsonIgnore` attribut. Les noms des propriétés sont également stockées dans le message. les noms de propriétés peuvent être abrégés à l’aide de la `JsonProperty` attribut. L’exemple de code suivant illustre comment exclure une propriété d’être envoyées au client et raccourcir les noms de propriété :

**Code de serveur .NET qui montre l’attribut JsonIgnore pour exclure des données envoyées au client et l’attribut JsonProperty pour réduire la taille de message**

[!code-csharp[Main](signalr-performance/samples/sample1.cs?highlight=5,7,10)]

Afin de conserver la lisibilité facilité de maintenance dans le code client, les noms de propriété abrégé peuvent être remappé vers convivial noms après la réception du message. L’exemple de code suivant illustre une façon possible de remappage des noms raccourcis à plus longues, en définissant un contrat de message (mappage) et à l’aide de la `reMap` fonction à appliquer le contrat à la classe de message optimisée :

**Code JavaScript côté client qui remappe réduit les noms de propriété aux noms contrôlable de visu**

[!code-javascript[Main](signalr-performance/samples/sample2.js)]

Noms peuvent être abrégés dans les messages à partir du client au serveur, à l’aide de la même méthode.

En diminuant l’encombrement de mémoire (autrement dit, la quantité de mémoire utilisée pour le message) du message d’objet peut également améliorer les performances. Par exemple, si la plage complète d’un `int` n’est pas nécessaire, un `short` ou `byte` peut être utilisé à la place.

Étant donné que les messages sont stockés dans le bus de messages dans la mémoire du serveur, ce qui réduit la taille des messages peut également résoudre les problèmes de mémoire de serveur.

<a id="tuning"></a>

### <a name="tuning-your-signalr-server-for-performance"></a>Réglage de votre serveur SignalR pour des performances

Les paramètres de configuration suivants peuvent être utilisés pour paramétrer votre serveur pour de meilleures performances dans une application SignalR. Pour obtenir des informations générales sur la façon d’améliorer les performances dans une application ASP.NET, consultez [amélioration des performances ASP.NET](https://msdn.microsoft.com/library/ff647787.aspx).

**Paramètres de configuration SignalR**

- **DefaultMessageBufferSize**: par défaut, SignalR conserve 1 000 messages en mémoire par le concentrateur par connexion. Si les messages volumineux sont utilisés, cela peut créer des problèmes de mémoire qui peuvent être Soulagés par la réduction de cette valeur. Ce paramètre peut être défini dans le `Application_Start` Gestionnaire d’événements dans une application ASP.NET, ou dans le `Configuration` méthode d’une classe de démarrage OWIN dans une application auto-hébergée. L’exemple suivant montre comment réduire cette valeur afin de réduire l’encombrement mémoire de votre application afin de réduire la quantité de mémoire du serveur :

    **Code de serveur .NET dans Global.asax pour diminuer la taille de mémoire tampon de message par défaut**

    [!code-csharp[Main](signalr-performance/samples/sample3.cs)]

**Paramètres de configuration IIS**

- **Nombre maximal de demandes simultanées par application**: augmenter le nombre de IIS simultanées demandes augmente les ressources serveur disponibles pour le traitement des demandes. La valeur par défaut est 5000 ; Pour augmenter ce paramètre, exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges :

    [!code-console[Main](signalr-performance/samples/sample4.cmd)]

**Paramètres de configuration ASP.NET**

Cette section inclut des paramètres de configuration qui peuvent être définies dans le `aspnet.config` fichier. Ce fichier se trouve dans un des deux emplacements, en fonction de la plateforme :

- `%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet.config`
- `%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet.config`

Les paramètres ASP.NET qui peuvent améliorer les performances de SignalR sont les suivantes :

- **Nombre maximal de demandes simultané par UC**: augmentation de ce paramètre peut atténuer les goulets d’étranglement. Pour augmenter ce paramètre, ajoutez le paramètre de configuration suivant à la `aspnet.config` fichier :

    [!code-xml[Main](signalr-performance/samples/sample5.xml?highlight=4)]
- **Limite de la file d’attente des requêtes**: lorsque le nombre total de connexions dépasse les `maxConcurrentRequestsPerCPU` définition, ASP.NET commence à l’aide d’une file d’attente de demandes de limitation. Pour augmenter la taille de la file d’attente, vous pouvez augmenter la `requestQueueLimit` paramètre. Pour ce faire, ajoutez le paramètre de configuration suivant à la `processModel` nœud `config/machine.config` (plutôt que `aspnet.config`) :

    [!code-xml[Main](signalr-performance/samples/sample6.xml)]

<a id="troubleshooting"></a>

## <a name="troubleshooting-performance-issues"></a>Résolution des problèmes de performances

Cette section décrit les façons de rechercher les goulots d’étranglement dans votre application.

### <a name="verifying-that-websocket-is-being-used"></a>Vérification que WebSocket est utilisé

SignalR pouvez utiliser une variété de transports pour la communication entre le client et le serveur, WebSocket offre de meilleures performances significatif et doit être utilisé pour le client et le serveur de prise en charge. Pour déterminer si votre client et le serveur de répondre à la configuration requise pour le WebSocket, consultez [du transport et secours](../getting-started/introduction-to-signalr.md#transports). Pour déterminer que le transport est utilisé dans votre application, vous pouvez utiliser les outils de développement de navigateur et examinez les journaux pour vérifier que le transport est utilisé pour la connexion. Pour plus d’informations sur l’utilisation des outils de développement de navigateur dans Internet Explorer et Chrome, consultez [du transport et secours](../getting-started/introduction-to-signalr.md#transports).

<a id="perfcounters"></a>

## <a name="using-signalr-performance-counters"></a>À l’aide des compteurs de performance SignalR

Cette section décrit comment activer et utiliser des compteurs de performance SignalR, trouvé dans le `Microsoft.AspNet.SignalR.Utils` package.

### <a name="installing-signalrexe"></a>Signalr.exe lors de l’installation

Compteurs de performances peuvent être ajoutés au serveur à l’aide d’un utilitaire appelé SignalR.exe. Pour installer cet utilitaire, procédez comme suit :

1. Dans votre application Visual Studio, sélectionnez **outils**, **Gestionnaire de Package de bibliothèque**, **gérer les Packages NuGet pour la Solution...**
2. Recherchez **signalr.utils**, puis sélectionnez Installer.

    ![](signalr-performance/_static/image1.png)
3. Acceptez le contrat de licence pour installer le package.
4. SignalR.exe sera installé dans `<project folder>/packages/Microsoft.AspNet.SignalR.Utils.<version>/tools`.

### <a name="installing-performance-counters-with-signalrexe"></a>Installation des compteurs de performance avec SignalR.exe

Pour installer les compteurs de performance SignalR, exécutez SignalR.exe dans une invite de commandes avec élévation de privilèges avec le paramètre suivant :

[!code-console[Main](signalr-performance/samples/sample7.cmd)]

Pour supprimer les compteurs de performance SignalR, exécutez SignalR.exe dans une invite de commandes avec élévation de privilèges avec le paramètre suivant :

[!code-console[Main](signalr-performance/samples/sample8.cmd)]

### <a name="signalr-performance-counters"></a>Compteurs de Performance de SignalR

Le package d’utilitaires installe des compteurs de performances suivants. Les compteurs « Total » mesurent le nombre d’événements depuis le dernier pool d’applications ou le serveur de redémarrer.

**Métriques de connexion**

Les métriques suivantes mesurent les événements de durée de vie de connexion qui se produisent. Pour plus d’informations, consultez [compréhension et gestion des événements de durée de vie de connexion](../guide-to-the-api/handling-connection-lifetime-events.md).

- **Connexions connectées**
- **Connexions reconnectées**
- **Connexions Disonnected**
- **Connexions en cours**

**Métriques de message**

Les métriques suivantes mesurent le trafic des messages généré par SignalR.

- **Nombre Total de Messages reçus connexion**
- **Nombre Total de Messages de connexion envoyés**
- **Connexion de Messages reçus/s**
- **Connexion de Messages envoyés/s**

**Métriques de bus de messages**

Les métriques suivantes mesurent le trafic via le bus de messages SignalR interne, la file d’attente dans laquelle tous les messages de SignalR entrants et sortants sont placés. Un message est **publié** lorsqu’il est envoyé ou de diffusion. A **abonné** dans ce contexte est un abonnement sur le bus de messages ; il doit être égal au nombre de clients plus le serveur lui-même. Un **allouée de travail** est un composant qui envoie des données pour les connexions actives ; un **travail occupé** est une tâche qui envoie un message activement.

- **Total reçu des Messages de Bus de messages**
- **Bus de messages des Messages reçus/s**
- **Bus de messages Messages publiés Total**
- **Bus de messages des Messages publiés par seconde**
- **Message Bus abonnés actuel**
- **Nombre Total d’abonnés message Bus**
- **Message Bus Subscribers/Sec**
- **Bus de messages allouée travailleurs**
- **Threads de travail occupés de Bus de messages**
- **Actuel de rubriques de Bus de messages**

**Métriques de l’erreur**

Les métriques suivantes mesurent les erreurs générées par le trafic des messages SignalR. **Résolution de concentrateur** erreurs se produisent quand un concentrateur ou une méthode de concentrateur ne peut pas être résolue. **Appel de concentrateur** erreurs sont des exceptions levées lors de l’appel d’une méthode de concentrateur. **Transport** erreurs sont des erreurs de connexion levées lors d’une demande HTTP ou d’une réponse.

- **Erreurs : Total de toutes les**
- **Erreurs : All/s**
- **Erreurs : Total de résolution Hub**
- **Erreurs : Résolution de concentrateur par seconde**
- **Erreurs : Total de l’appel Hub**
- **Erreurs : Appel de concentrateur par seconde**
- **Erreurs : Total de Transport**
- **Erreurs : Transport par seconde**

**Métriques de montée en puissance parallèle**

Les métriques suivantes mesurent le trafic et les erreurs générées par le fournisseur de montée en puissance parallèle. A **flux** dans ce contexte est une unité d’échelle utilisée par le fournisseur de montée en charge ; il s’agit une table si SQL Server est utilisée, une rubrique si Service Bus est utilisé et un abonnement si Redis est utilisé. Par défaut, un seul flux est utilisé, mais vous pouvez l’augmenter par la configuration sur SQL Server et Service Bus. A **mise en mémoire tampon** flux de données est celui qui a entré un état d’erreur ; lorsque le flux de données est dans l’état par défaut, tous les messages envoyés au fond de panier échoue immédiatement jusqu'à ce que le flux de données n’est plus défaillante. Le **longueur de file d’attente d’envoi** est le nombre de messages qui ont été validées mais pas encore été envoyées.

- **Bus de messages de montée en charge les Messages reçus/s**
- **Nombre Total de flux de montée en puissance parallèle**
- **Ouvrir des flux de montée en puissance parallèle**
- **Mise en mémoire tampon de flux de montée en puissance parallèle**
- **Total des erreurs de montée en puissance parallèle**
- **Erreurs de montée en puissance parallèle par seconde**
- **Longueur de file d’attente d’envoi de montée en puissance parallèle**

Pour plus d’informations sur ce que ces compteurs sont de mesure, consultez [SignalR Scaleout avec Azure Service Bus](scaleout-with-windows-azure-service-bus.md).

<a id="othercounters"></a>

## <a name="using-other-performance-counters"></a>À l’aide d’autres compteurs de performances

Les compteurs de performances suivants peuvent également être utiles pour surveiller les performances de votre application.

**Memory**

- .NET CLR mémoire Nb d’octets dans tous les segments de mémoire (pour w3wp)

**ASP.NET**

- ASP. net\requêtes en cours
- ASP.NET\Queued
- ASP.NET\Rejected

**CPU**

- Temps de processeur Information\Processor

**TCP/IP**

- TCPv6/connexions établies
- TCPv4/connexions établies

**Le Service Web**

- Connexions Web service web\connexions en cours
- Connexions Web Service\Maximum

**Thread**

- .NET CLR LocksAndThreads\# de Threads actuels logiques
- .NET CLR LocksAnd Threads\# de Threads actuels physiques

<a id="otherresources"></a>

## <a name="other-resources"></a>Autres ressources

Pour plus d’informations sur les performances d’ASP.NET la surveillance et de paramétrage, consultez les rubriques suivantes :

- [Vue d’ensemble des performances ASP.NET](https://msdn.microsoft.com/library/cc668225(v=vs.100).aspx)
- [Utilisation de Thread ASP.NET sur IIS 6.0, IIS 7.0 et IIS 7.5](https://blogs.msdn.com/b/tmarq/archive/2007/07/21/asp-net-thread-usage-on-iis-7-0-and-6-0.aspx)
- [&lt;applicationPool&gt; élément (paramètres Web)](https://msdn.microsoft.com/library/dd560842.aspx)

---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-net-client
title: "Guide d’API ASP.NET SignalR concentrateurs - Client .NET (c#) | Documents Microsoft"
author: pfletcher
description: "Ce document fournit une introduction à l’utilisation de l’API de Hubs pour SignalR version 2, les clients .NET, tels que le Windows Store (WinRT), WPF, Silverlight et les inconvénients..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: 6d02d9f7-94e5-4140-9f51-5a6040f274f6
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: c52a02291e18b1dd8a9d95b33fe466d17aae835f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-signalr-hubs-api-guide---net-client-c"></a>Guide d’API ASP.NET SignalR concentrateurs - Client .NET (c#)
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

> Ce document fournit une introduction à l’utilisation de l’API de Hubs pour SignalR version 2, les clients .NET, tels que le Windows Store (WinRT), WPF, Silverlight et les applications de console.
> 
> L’API de concentrateurs SignalR vous permet de vous permettent d’effectuer des appels de procédure distante (RPC) à partir d’un serveur pour les clients connectés et à partir de clients sur le serveur. Dans le code serveur, vous définissez les méthodes qui peuvent être appelées par les clients, et vous appelez des méthodes qui s’exécutent sur le client. Dans le code client, vous définissez les méthodes qui peuvent être appelées à partir du serveur, et vous appelez des méthodes qui s’exécutent sur le serveur. SignalR prend en charge de tous les éléments client-serveur pour vous.
> 
> SignalR offre également une API de niveau inférieur appelée connexions persistantes. Pour obtenir une présentation SignalR, des concentrateurs et des connexions persistantes, ou pour obtenir un didacticiel qui montre comment générer une application SignalR complète, consultez [SignalR - mise en route](../getting-started/index.md).
> 
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


## <a name="overview"></a>Vue d'ensemble

Ce document contient les sections suivantes :

- [Programme d’installation du client](#clientsetup)
- [Comment établir une connexion](#establishconnection)

    - [Connexions entre les domaines à partir de clients Silverlight](#slcrossdomain)
- [Comment configurer la connexion](#configureconnection)

    - [Comment définir le nombre maximal de connexions simultanées dans les clients WPF](#maxconnections)
    - [Comment spécifier des paramètres de chaîne de requête](#querystring)
    - [Comment spécifier le mode de transport](#transport)
    - [Comment spécifier des en-têtes HTTP](#httpheaders)
    - [Comment spécifier des certificats clients](#clientcertificate)
- [Comment créer le concentrateur proxy.](#proxy)
- [Comment définir des méthodes sur le client que le serveur peut appeler.](#callclient)

    - [Méthodes sans paramètres](#clientmethodswithoutparms)
    - [Méthodes avec des paramètres, en spécifiant les types de paramètres](#clientmethodswithparmtypes)
    - [Méthodes avec des paramètres, en spécifiant des objets dynamiques pour les paramètres](#clientmethodswithdynamparms)
    - [Comment supprimer un gestionnaire](#removehandler)
- [Comment appeler des méthodes de serveur à partir du client](#callserver)
- [Comment gérer les événements de durée de vie de connexion](#connectionlifetime)
- [Comment gérer les erreurs](#handleerrors)
- [Comment activer la journalisation côté client](#logging)
- [Exemples de code d’application console pour les méthodes de client que le serveur peut appeler, WPF et Silverlight](#wpfsl)

Pour un projet de client exemple .NET, consultez les ressources suivantes :

- [Gustave-armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) sur GitHub.com (exemples d’application console de WinRT, Silverlight,).
- [DamianEdwards / SignalR-MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) sur GitHub.com (par exemple, WPF).
- [SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) sur GitHub.com (exemple d’application Console).

Pour plus d’informations sur la façon de programmer le serveur ou les clients JavaScript, consultez les ressources suivantes :

- [Guide d’API concentrateurs SignalR - serveur](hubs-api-guide-server.md)
- [Guide d’API concentrateurs SignalR - Client JavaScript](hubs-api-guide-javascript-client.md)

Des liens vers des rubriques de référence de l’API sont à la version de .NET 4.5 de l’API. Si vous utilisez le .NET 4, consultez [la version de .NET 4 des rubriques API](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).

<a id="clientsetup"></a>

## <a name="client-setup"></a>Programme d’installation du client

Installer le [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) package NuGet (pas le [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) package). Ce package prend en charge WinRT, Silverlight, WPF, les applications console et les clients Windows Phone, pour .NET 4 et 4.5 de .NET.

Si la version de SignalR que vous disposez sur le client est différente de la version que vous disposez sur le serveur, SignalR est souvent capable de s’adapter à la différence. Par exemple, un serveur exécutant la version 2 de SignalR prendra en charge les clients qui ont installé des 1.1.x, ainsi que les clients dont la version 2 est installé. Si la différence entre la version sur le serveur et la version sur le client est trop élevée, ou si le client est plus récent que le serveur, SignalR lève une `InvalidOperationException` exception lorsque le client tente d’établir une connexion. Le message d’erreur est «`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`».

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Comment établir une connexion

Avant de pouvoir établir une connexion, vous devez créer un `HubConnection` de l’objet et créer un proxy. Pour établir la connexion, appelez le `Start` méthode sur le `HubConnection` objet.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> Pour les clients JavaScript que vous inscrivez au moins un gestionnaire d’événements avant d’appeler le `Start` méthode pour établir la connexion. Cela n’est pas nécessaire pour les clients .NET. Pour les clients JavaScript, le code proxy généré crée automatiquement des proxys pour tous les Hubs qui existent sur le serveur et l’inscription d’un gestionnaire est de vous indiquez comment les concentrateurs de votre client prévoit d’utiliser. Mais pour un client .NET vous créer des proxys de Hub manuellement, donc SignalR part du principe que vous utilisez n’importe quel concentrateur que vous créez un proxy pour.


L’exemple de code utilise la valeur par défaut « / signalr « URL pour se connecter à votre service de SignalR. Pour plus d’informations sur la façon de spécifier une autre URL de base, consultez [ASP.NET SignalR concentrateurs API Guide - Server - l’URL /signalr](hubs-api-guide-server.md#signalrurl).

Le `Start` méthode s’exécute de façon asynchrone. Pour vous assurer que les lignes de code ne s’exécutent jusqu'à une fois la connexion établie, utilisez `await` dans une méthode asynchrone ASP.NET 4.5 ou `.Wait()` dans une méthode synchrone. N’utilisez pas `.Wait()` dans un client WinRT.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a>Connexions entre les domaines à partir de clients Silverlight

Pour plus d’informations sur la façon d’activer les connexions inter-domaines à partir de clients de Silverlight, consultez [rendre un Service disponible entre les limites de domaine](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Comment configurer la connexion

Avant d’établir une connexion, vous pouvez spécifier une des options suivantes :

- Limite de connexions simultanées.
- Les paramètres de chaîne de requête.
- Le mode de transport.
- En-têtes HTTP.
- Certificats clients.

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a>Comment définir le nombre maximal de connexions simultanées dans les clients WPF

Clients de WPF, vous devrez peut-être augmenter le nombre maximal de connexions simultanées à partir de sa valeur par défaut de 2. La valeur recommandée est 10.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

Pour plus d’informations, consultez [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Comment spécifier des paramètres de chaîne de requête

Si vous souhaitez envoyer des données sur le serveur lorsque le client se connecte, vous pouvez ajouter des paramètres de chaîne de requête à l’objet de connexion. L’exemple suivant montre comment définir un paramètre de chaîne de requête dans le code client.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample5.cs)]

L’exemple suivant montre comment lire un paramètre de chaîne de requête dans le code serveur.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Comment spécifier le mode de transport

Dans le cadre du processus de connexion, un client SignalR est normalement négocie avec le serveur pour déterminer le transport meilleure prise en charge par le serveur et client. Si vous savez déjà que vous souhaitez utiliser le transport, vous pouvez ignorer ce processus de négociation. Pour spécifier le mode de transport, passez dans un objet de transport à la méthode Start. L’exemple suivant montre comment spécifier le mode de transport dans le code client.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

Le [Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) espace de noms inclut les classes suivantes qui vous permet de spécifier le transport.

- [LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)
- [ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)
- [WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (disponible uniquement lorsque le serveur et le client utilisent .NET 4.5).
- [AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (choisit automatiquement le transport meilleure prise en charge par le client et le serveur. Il s’agit du transport par défaut. En la passant dans pour la `Start` méthode a le même effet que passant ne pas quoi que ce soit.)

Le transport ForeverFrame n’est pas inclus dans cette liste, car elle est utilisée uniquement par les navigateurs.

Pour plus d’informations sur la vérification de la méthode de transport dans le code serveur, consultez [ASP.NET SignalR concentrateurs API Guide - Server - comment obtenir des informations sur le client à partir de la propriété de contexte](hubs-api-guide-server.md#contextproperty). Pour plus d’informations sur les transports et de secours, consultez [Introduction à SignalR - Transports et secours](../getting-started/introduction-to-signalr.md#transports).

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a>Comment spécifier des en-têtes HTTP

Pour définir des en-têtes HTTP, utilisez le `Headers` propriété sur l’objet de connexion. L’exemple suivant montre comment ajouter un en-tête HTTP.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a>Comment spécifier des certificats clients

Pour ajouter des certificats clients, utilisez la `AddClientCertificate` méthode sur l’objet de connexion.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a>Comment créer le concentrateur proxy.

Pour définir des méthodes sur le client un concentrateur peut appeler à partir du serveur et pour appeler des méthodes sur un concentrateur au niveau du serveur, créez un proxy pour le concentrateur en appelant `CreateHubProxy` sur l’objet de connexion. La chaîne vous passez dans `CreateHubProxy` est le nom de votre classe de concentrateur ou le nom spécifié par le `HubName` attribut si celui utilisé sur le serveur. Correspondance de nom respecte la casse.

**Classe de concentrateur sur le serveur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

**Créer le proxy client pour la classe de concentrateur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

Si vous ajoutez votre classe Hub avec un `HubName` d’attribut, utilisez ce nom.

**Classe de concentrateur sur le serveur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample12.cs)]

**Créer le proxy client pour la classe de concentrateur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

Si vous appelez `HubConnection.CreateHubProxy` plusieurs fois avec le même `hubName`, vous obtenez la même mise en cache `IHubProxy` objet.

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Comment définir des méthodes sur le client que le serveur peut appeler.

Pour définir une méthode que le serveur peut appeler, utilisez du proxy `On` méthode pour inscrire un gestionnaire d’événements.

Correspondance de nom de méthode respecte la casse. Par exemple, `Clients.All.UpdateStockPrice` sur le serveur s’exécute `updateStockPrice`, `updatestockprice`, ou `UpdateStockPrice` sur le client.

Plates-formes clientes différentes ont des exigences différentes pour la façon dont vous écrivez le code de la méthode pour mettre à jour l’interface utilisateur. Les exemples présentés sont pour les clients de WinRT (Windows Store .NET). Exemples d’application console, WPF et Silverlight sont fournies dans [une section distincte, plus loin dans cette rubrique](#wpfsl).

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a>Méthodes sans paramètres

Si la méthode que vous traitez n’a pas de paramètres, utilisez la surcharge non générique de la `On` méthode :

**Code de serveur client de méthode sans paramètres**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

**Code de WinRT Client pour la méthode est appelée depuis le serveur sans paramètres ([voir des exemples WPF et Silverlight plus loin dans cette rubrique](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>Méthodes avec des paramètres, en spécifiant les types de paramètres

Si la méthode que vous traitez possède des paramètres, spécifiez les types des paramètres en tant que les types génériques de la `On` (méthode). Il existe des surcharges génériques de la `On` méthode pour vous permettre de spécifier des paramètres jusqu'à 8 (4 sur Windows Phone 7). Dans l’exemple suivant, un paramètre est envoyé à la `UpdateStockPrice` (méthode).

**Appel de méthode du client avec un paramètre de code de serveur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

**La classe de Stock utilisée pour le paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample17.cs)]

**Code de WinRT Client pour une méthode appelée depuis le serveur avec un paramètre ([voir des exemples WPF et Silverlight plus loin dans cette rubrique](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>Méthodes avec des paramètres, en spécifiant des objets dynamiques pour les paramètres

En guise d’alternative à la spécification des paramètres comme des types génériques de la `On` (méthode), vous pouvez spécifier des paramètres en tant qu’objets dynamiques :

**Appel de méthode du client avec un paramètre de code de serveur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

**La classe de Stock utilisée pour le paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample20.cs)]

**Code de WinRT Client pour une méthode appelée depuis le serveur avec un paramètre, à l’aide d’un objet dynamique pour le paramètre ([voir des exemples WPF et Silverlight plus loin dans cette rubrique](#wpfsl))**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a>Comment supprimer un gestionnaire

Pour supprimer un gestionnaire, appelez sa `Dispose` (méthode).

**Code client pour une méthode appelée à partir du serveur**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

**Code client pour supprimer le Gestionnaire**

[!code-css[Main](hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Comment appeler des méthodes de serveur à partir du client

Pour appeler une méthode sur le serveur, utilisez la `Invoke` méthode sur le concentrateur proxy.

Si la méthode serveur n’a aucune valeur de retour, utilisez la surcharge non générique de la `Invoke` (méthode).

**Code de serveur pour une méthode qui n’a aucune valeur de retour**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

**Code client appelant une méthode qui n’a aucune valeur de retour**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

Si la méthode serveur a une valeur de retour, spécifiez le type de retour que le type générique de la `Invoke` (méthode).

**Code de serveur pour une méthode qui a une valeur de retour et prend un paramètre de type complexe**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

**La classe de Stock utilisée pour le paramètre et la valeur de retour**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample27.cs)]

**Code client appelant une méthode qui a une valeur de retour et prend un paramètre de type complexe dans une méthode async de ASP.NET 4.5**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

**Code client appelant une méthode qui a une valeur de retour et prend un paramètre de type complexe dans une méthode synchrone**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

Le `Invoke` méthode exécute de façon asynchrone et retourne un `Task` objet. Si vous ne spécifiez pas `await` ou `.Wait()`, la ligne suivante de code sera exécuté avant que la méthode que vous appelez a terminé l’exécution.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Comment gérer les événements de durée de vie de connexion

SignalR fournit des événements de durée de vie que vous pouvez gérer la connexion suivante :

- `Received`: Déclenché lorsque des données sont reçues sur la connexion. Fournit les données reçues.
- `ConnectionSlow`: Déclenché lorsque le client détecte une connexion lente ou suppression fréquemment.
- `Reconnecting`: Déclenché lorsque le transport sous-jacent commence la reconnexion.
- `Reconnected`: Déclenché lorsque le transport sous-jacent s’est reconnecté.
- `StateChanged`: Déclenché lorsque l’état de la connexion change. Fournit l’ancien état et le nouvel état. Pour plus d’informations sur la connexion valeurs d’état, consultez [énumération ConnectionState](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).
- `Closed`: Déclenché lors de la connexion s’est déconnecté.

Par exemple, si vous souhaitez afficher les messages d’avertissement pour les erreurs qui ne sont pas irrécupérable mais entraînent des problèmes de connexion intermittents, par exemple que la lenteur ou fréquentes la suppression de la connexion, gèrent les `ConnectionSlow` événement.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample30.cs)]

Pour plus d’informations, consultez [compréhension et gestion des événements de durée de vie de connexion dans SignalR](handling-connection-lifetime-events.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Comment gérer les erreurs

Si vous n’activez pas explicitement des messages d’erreur détaillés sur le serveur, l’objet exception qui SignalR renvoie une erreur et contient un minimum d’informations sur l’erreur. Par exemple, si un appel à `newContosoChatMessage` échoue, le message d’erreur dans l’objet d’erreur contient «`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`« envoi de messages d’erreur détaillés pour les clients en production n’est pas recommandé pour des raisons de sécurité, mais si vous souhaitez activer les messages d’erreur détaillés pour à des fins de résolution des problèmes, utilisez le code suivant sur le serveur.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

Pour gérer les erreurs SignalR déclenche, vous pouvez ajouter un gestionnaire pour le `Error` événement sur l’objet de connexion.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample32.cs)]

Pour gérer les erreurs d’appels de méthode, encapsulez le code dans un bloc try-catch.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Comment activer la journalisation côté client

Pour activer la journalisation côté client, définissez la `TraceLevel` et `TraceWriter` propriétés de l’objet de connexion.

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a>Exemples de code d’application console pour les méthodes de client que le serveur peut appeler, WPF et Silverlight

Les exemples de code ci-dessus pour définir des méthodes de client que le serveur peut appeler s’appliquent aux clients de WinRT. Les exemples suivants montrent le code équivalent pour les clients d’application console, WPF et Silverlight.

### <a name="methods-without-parameters"></a>Méthodes sans paramètres

**Code de client WPF pour la méthode appelée à partir du serveur sans paramètres**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

**Code de client Silverlight pour la méthode est appelée depuis le serveur sans paramètres**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

**Code du client d’application console pour la méthode est appelée depuis le serveur sans paramètres**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a>Méthodes avec des paramètres, en spécifiant les types de paramètres

**Code de client WPF pour une méthode appelée depuis le serveur avec un paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

**Code de client Silverlight pour une méthode appelée depuis le serveur avec un paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

**Code du client d’application console pour une méthode appelée depuis le serveur avec un paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a>Méthodes avec des paramètres, en spécifiant des objets dynamiques pour les paramètres

**Code de client WPF pour une méthode appelée depuis le serveur avec un paramètre, à l’aide d’un objet dynamique pour le paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

**Code de client Silverlight pour une méthode appelée depuis le serveur avec un paramètre, à l’aide d’un objet dynamique pour le paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

**Code du client d’application console pour une méthode appelée depuis le serveur avec un paramètre, à l’aide d’un objet dynamique pour le paramètre**

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]

---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-server
title: "Guide d’API ASP.NET SignalR concentrateurs - serveur (c#) | Documents Microsoft"
author: pfletcher
description: "Ce document fournit une introduction à la programmation côté serveur de l’API de concentrateurs SignalR ASP.NET pour SignalR version 2, avec des exemples de code illustrant..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: b19913e5-cd8a-4e4b-a872-5ac7a858a934
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-server
msc.type: authoredcontent
ms.openlocfilehash: c2567d4d39a494daf77a23db5dff83c8fae4925d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-signalr-hubs-api-guide---server-c"></a>Guide d’API ASP.NET SignalR concentrateurs - serveur (c#)
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

> Ce document fournit une introduction à la programmation côté serveur de l’API de concentrateurs SignalR ASP.NET pour SignalR version 2, avec des exemples de code illustrant les options courantes.
> 
> L’API de concentrateurs SignalR vous permet de vous permettent d’effectuer des appels de procédure distante (RPC) à partir d’un serveur pour les clients connectés et à partir de clients sur le serveur. Dans le code serveur, vous définissez les méthodes qui peuvent être appelées par les clients, et vous appelez des méthodes qui s’exécutent sur le client. Dans le code client, vous définissez les méthodes qui peuvent être appelées à partir du serveur, et vous appelez des méthodes qui s’exécutent sur le serveur. SignalR prend en charge de tous les éléments client-serveur pour vous.
> 
> SignalR offre également une API de niveau inférieur appelée connexions persistantes. Pour obtenir une présentation SignalR, des concentrateurs et des connexions persistantes, consultez [Introduction à SignalR 2](../getting-started/introduction-to-signalr.md).
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
> ## <a name="topic-versions"></a>Versions de la rubrique
> 
> Pour plus d’informations sur les versions antérieures de SignalR, consultez [SignalR des Versions antérieures](../older-versions/index.md).
> 
> ## <a name="questions-and-comments"></a>Questions et des commentaires
> 
> Veuillez laisser des commentaires sur la façon dont vous avez aimé ce didacticiel et nous pouvons améliorer dans les commentaires en bas de la page. Si vous avez des questions qui ne sont pas directement liées à ce didacticiel, vous pouvez les valider pour le [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) ou [StackOverflow.com](http://stackoverflow.com/).


## <a name="overview"></a>Vue d'ensemble

Ce document contient les sections suivantes :

- [Comment inscrire SignalR intergiciel (middleware)](#route)

    - [L’URL /signalr](#signalrurl)
    - [Configuration des options de SignalR](#options)
- [Comment créer et utiliser des classes de Hub](#hubclass)

    - [Durée de vie Hub](#transience)
    - [Casse mixte des noms de concentrateur dans les clients JavaScript](#hubnames)
    - [Plusieurs concentrateurs](#multiplehubs)
    - [Concentrateurs de fortement typé](#stronglytypedhubs)
- [Comment définir des méthodes dans la classe de concentrateur que les clients peuvent appeler.](#hubmethods)

    - [Casse mixte des noms de méthode dans les clients JavaScript](#methodnames)
    - [Quand exécuter de façon asynchrone](#asyncmethods)
    - [Définir des surcharges](#overloads)
    - [Signaler la progression à partir des appels de méthode de concentrateur](#progress)
- [Comment appeler des méthodes de client à partir de la classe de concentrateur](#callfromhub)

    - [En sélectionnant les clients qui reçoivent l’appel RPC](#selectingclients)
    - [Aucune validation de la compilation pour les noms de méthode](#dynamicmethodnames)
    - [Une correspondance de nom sans respecter la casse (méthode)](#caseinsensitive)
    - [Exécution asynchrone](#asyncclient)
- [Comment gérer l’appartenance au groupe à partir de la classe de concentrateur](#groupsfromhub)

    - [Exécution asynchrone de méthodes Add et Remove](#asyncgroupmethods)
    - [Persistance de l’appartenance au groupe](#grouppersistence)
    - [Groupes d’utilisateur unique](#singleusergroups)
- [Comment gérer les événements de durée de vie de connexion dans la classe de concentrateur](#connectionlifetime)

    - [Lorsque les OnConnected, OnDisconnected et OnReconnected sont appelées](#onreconnected)
    - [État de l’appelant ne pas remplie](#nocallerstate)
- [Comment obtenir des informations sur le client à partir de la propriété de contexte](#contextproperty)
- [Comment passer l’état entre les clients et de la classe de concentrateur](#passstate)
- [Comment gérer les erreurs dans la classe de concentrateur](#handleErrors)
- [Comment appeler des méthodes de client et de gérer des groupes à l’extérieur de la classe de concentrateur](#callfromoutsidehub)

    - [Appel de méthodes de client](#callingclientsoutsidehub)
    - [La gestion de l’appartenance au groupe](#managinggroupsoutsidehub)
- [Comment activer le suivi](#tracing)
- [Comment personnaliser le pipeline de concentrateurs](#hubpipeline)

Pour plus d’informations sur comment les clients du programme, consultez les ressources suivantes :

- [Guide d’API concentrateurs SignalR - Client JavaScript](hubs-api-guide-javascript-client.md)
- [Guide d’API concentrateurs SignalR - Client .NET](hubs-api-guide-net-client.md)

Les composants serveur pour SignalR 2 sont uniquement disponibles dans .NET 4.5. Serveurs exécutant .NET 4.0 doivent utiliser version 1.x de SignalR.

<a id="route"></a>

## <a name="how-to-register-signalr-middleware"></a>Comment inscrire SignalR intergiciel (middleware)

Pour définir l’itinéraire que les clients utiliseront pour se connecter à votre concentrateur, appelez le `MapSignalR` méthode lorsque l’application démarre. `MapSignalR`est un [méthode d’extension](https://msdn.microsoft.com/library/vstudio/bb383977.aspx) pour la `OwinExtensions` classe. L’exemple suivant montre comment définir l’itinéraire de concentrateurs SignalR à l’aide d’une classe de démarrage OWIN.

[!code-csharp[Main](hubs-api-guide-server/samples/sample1.cs)]

Si vous ajoutez des fonctionnalités de SignalR pour une application ASP.NET MVC, assurez-vous que l’itinéraire SignalR est ajouté avant les autres itinéraires. Pour plus d’informations, consultez [didacticiel : mise en route avec SignalR 2 et MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).

<a id="signalrurl"></a>

### <a name="the-signalr-url"></a>L’URL /signalr

Par défaut, l’URL de routage qui les clients utiliseront pour se connecter à votre concentrateur est « / signalr ». (Ne confondez pas cette URL avec l’URL « concentrateurs signalr / / », qui est le fichier JavaScript généré automatiquement. Pour plus d’informations sur le proxy généré, consultez [Guide API de concentrateurs SignalR - JavaScript Client - du proxy généré et ce qu’il fait pour vous](hubs-api-guide-javascript-client.md#genproxy).)

Il peut y avoir des circonstances exceptionnelles qui rendent cette URL de base n’est pas utilisable pour SignalR ; par exemple, vous avez un dossier dans votre projet nommé *signalr* et vous ne souhaitez pas modifier le nom. Dans ce cas, vous pouvez modifier l’URL de base, comme indiqué dans les exemples suivants (remplacez « / signalr » dans l’exemple de code avec l’URL de votre choix).

**Code de serveur qui spécifie l’URL**

[!code-csharp[Main](hubs-api-guide-server/samples/sample2.cs?highlight=1)]

**Code JavaScript client qui spécifie l’URL (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-server/samples/sample3.js?highlight=1)]

**Code JavaScript client qui spécifie l’URL (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-server/samples/sample4.js?highlight=1)]

**Code client .NET qui spécifie l’URL**

[!code-csharp[Main](hubs-api-guide-server/samples/sample5.cs?highlight=1)]

<a id="options"></a>

### <a name="configuring-signalr-options"></a>Configuration des Options de SignalR

Les surcharges de la `MapSignalR` méthode permettent de spécifier une URL personnalisée, un résolveur de dépendance personnalisée et les options suivantes :

- Activer les appels inter-domaines à partir de clients de navigateur à l’aide de CORS ou JSONP.

    En général, si le navigateur charge une page à partir de `http://contoso.com`, la connexion SignalR est dans le même domaine, `http://contoso.com/signalr`. Si la page à partir de `http://contoso.com` établit une connexion à `http://fabrikam.com/signalr`, qui est une connexion entre domaines. Pour des raisons de sécurité, les connexions inter-domaines sont désactivées par défaut. Pour plus d’informations, consultez [ASP.NET SignalR concentrateurs API Guide - JavaScript Client - comment établir une connexion entre domaines](hubs-api-guide-javascript-client.md#crossdomain).
- Activer les messages d’erreur détaillés.

    Lorsque des erreurs se produisent, le comportement par défaut de SignalR consiste à envoyer aux clients un message de notification sans plus d’informations sur ce qui est arrivé. Envoi des informations détaillées sur les clients n’est pas recommandée dans la production, car les utilisateurs malveillants peuvent être en mesure d’utiliser les informations à des attaques contre votre application. Pour le dépannage, vous pouvez utiliser cette option pour activer temporairement le rapport d’erreurs plus détaillées.
- Désactiver les fichiers de proxy JavaScript générés automatiquement.

    Par défaut, un fichier JavaScript avec les serveurs proxy pour les classes de votre Hub est généré en réponse à l’URL « concentrateurs signalr / / ». Si vous ne souhaitez pas utiliser les proxy JavaScript, ou si vous souhaitez générer ce fichier manuellement et faire référence à un fichier physique de vos clients, vous pouvez utiliser cette option pour désactiver la génération de proxy. Pour plus d’informations, consultez [Guide API de concentrateurs SignalR - JavaScript Client - proxy généré de la création d’un fichier physique pour SignalR](hubs-api-guide-javascript-client.md#manualproxy).

L’exemple suivant montre comment spécifier l’URL de connexion SignalR et ces options dans un appel à la `MapSignalR` (méthode). Pour spécifier une URL personnalisée, remplacez « / signalr » dans l’exemple par l’URL que vous souhaitez utiliser.

[!code-csharp[Main](hubs-api-guide-server/samples/sample6.cs)]

<a id="hubclass"></a>

## <a name="how-to-create-and-use-hub-classes"></a>Comment créer et utiliser des classes de Hub

Pour créer un concentrateur, créez une classe qui dérive de [Microsoft.Aspnet.Signalr.Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx). L’exemple suivant montre une classe simple de Hub pour une application de conversation.

[!code-csharp[Main](hubs-api-guide-server/samples/sample7.cs)]

Dans cet exemple, un client connecté peut appeler le `NewContosoChatMessage` (méthode), et dans ce cas, les données reçues sont envoyées à tous les clients connectés.

<a id="transience"></a>

### <a name="hub-object-lifetime"></a>Durée de vie Hub

Vous n’instanciez la classe de concentrateur ou appeler ses méthodes à partir de votre propre code sur le serveur ; tout ce qui est réalisée pour vous par le pipeline de concentrateurs SignalR. SignalR crée une nouvelle instance de votre classe de concentrateur chaque fois qu’il faut gérer une opération de concentrateur par exemple quand un client se connecte, se déconnecte ou effectue une appel de méthode sur le serveur.

Étant donné que les instances de la classe de concentrateur sont temporaires, vous ne pouvez pas les utiliser pour gérer l’état à partir d’un appel de méthode à l’autre. Chaque fois que le serveur reçoit un appel de méthode à partir d’un client, une nouvelle instance de votre processus de classe de concentrateur le message. Pour conserver l’état via plusieurs connexions et les appels de méthode, utilisez une autre méthode, tel qu’une base de données, ou une variable statique sur la classe de concentrateur ou une autre classe ne dérive pas de `Hub`. Si vous conserver les données en mémoire, à l’aide d’une méthode comme une variable statique sur la classe de concentrateur, les données seront perdues lors du recyclage du domaine d’application.

Si vous souhaitez envoyer des messages aux clients de votre propre code qui s’exécute en dehors de la classe de concentrateur, vous ne peut pas le faire en instanciant une instance de classe de concentrateur, mais vous pouvez le faire en obtenant une référence à l’objet de contexte SignalR pour votre classe de concentrateur. Pour plus d’informations, consultez [comment appeler des méthodes de client et de gérer des groupes à l’extérieur de la classe de concentrateur](#callfromoutsidehub) plus loin dans cette rubrique.

<a id="hubnames"></a>

### <a name="camel-casing-of-hub-names-in-javascript-clients"></a>Casse mixte des noms de concentrateur dans les clients JavaScript

Par défaut, les clients JavaScript font référence à des concentrateurs à l’aide d’une version de casse mixte du nom de classe. SignalR effectue automatiquement cette modification afin que le code JavaScript peut être conforme aux conventions de JavaScript. L’exemple précédent est désigné en tant que `contosoChatHub` dans le code JavaScript.

**Server**

[!code-csharp[Main](hubs-api-guide-server/samples/sample8.cs?highlight=1)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample9.js?highlight=1)]

Si vous souhaitez spécifier un autre nom pour les clients, ajoutez le `HubName` attribut. Lorsque vous utilisez un `HubName` d’attribut, il n’existe aucun changement de nom en casse mixte sur les clients JavaScript.

**Server**

[!code-csharp[Main](hubs-api-guide-server/samples/sample10.cs?highlight=1)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample11.js?highlight=1)]

<a id="multiplehubs"></a>

### <a name="multiple-hubs"></a>Plusieurs concentrateurs

Vous pouvez définir plusieurs classes de Hub dans une application. Lorsque vous procédez ainsi, la connexion est partagée, mais les groupes sont distincts :

- Tous les clients utiliseront la même URL pour établir une connexion SignalR avec votre service (« / signalr » ou l’URL personnalisée si vous avez défini un), et que la connexion est utilisée pour tous les Hubs définis par le service.

    Il n’existe aucune différence de performances pour plusieurs concentrateurs par rapport à la définition de toutes les fonctionnalités de concentrateur dans une classe unique.
- Tous les concentrateurs d’Obtient les mêmes informations de demande HTTP.

    Étant donné que tous les concentrateurs partagent la même connexion, les informations de demande HTTP uniquement le serveur obtient sont ce qui est fourni dans la requête HTTP d’origine qui établit la connexion de SignalR. Si vous utilisez la demande de connexion pour transmettre des informations à partir du client au serveur en spécifiant une chaîne de requête, vous ne peut pas fournir des chaînes de requête différents aux concentrateurs différents. Tous les concentrateurs recevront les mêmes informations.
- Le fichier de proxies JavaScript généré contiendra des proxys pour tous les Hubs dans un seul fichier.

    Pour plus d’informations sur les proxies JavaScript, consultez [Guide API de concentrateurs SignalR - JavaScript Client - du proxy généré et ce qu’il fait pour vous](hubs-api-guide-javascript-client.md#genproxy).
- Les groupes sont définis dans les concentrateurs.

    Dans SignalR, que vous pouvez définir des groupes pour diffuser à des sous-ensembles de clients connectés nommés. Les groupes sont gérées séparément pour chaque concentrateur. Par exemple, un groupe nommé « Administrateurs » inclut un ensemble de clients pour votre `ContosoChatHub` rapporte classe et le même nom de groupe à un autre ensemble de clients pour votre `StockTickerHub` classe.

<a id="stronglytypedhubs"></a>
### <a name="strongly-typed-hubs"></a>Concentrateurs de fortement typé

Pour définir une interface pour les méthodes de concentrateur votre client peut référence (et activer Intellisense dans vos méthodes de concentrateur), dérivez votre concentrateur `Hub<T>` (introduite dans SignalR 2.1) plutôt que `Hub`:

[!code-csharp[Main](hubs-api-guide-server/samples/sample12.cs)]

<a id="hubmethods"></a>

## <a name="how-to-define-methods-in-the-hub-class-that-clients-can-call"></a>Comment définir des méthodes dans la classe de concentrateur que les clients peuvent appeler.

Pour exposer une méthode sur le concentrateur que vous souhaitez pouvoir être appelée à partir du client, déclarez une méthode publique, comme indiqué dans les exemples suivants.

[!code-csharp[Main](hubs-api-guide-server/samples/sample13.cs?highlight=3)]

[!code-csharp[Main](hubs-api-guide-server/samples/sample14.cs?highlight=3)]

Vous pouvez spécifier un type de retour et des paramètres, y compris les types complexes et les tableaux, comme vous le feriez dans n’importe quelle méthode c#. Toutes les données dans les paramètres de réception ou de retourner à l’appelant sont communiquées entre le client et le serveur à l’aide de JSON et SignalR gère la liaison d’objets complexes et les tableaux d’objets automatiquement.

<a id="methodnames"></a>

### <a name="camel-casing-of-method-names-in-javascript-clients"></a>Casse mixte des noms de méthode dans les clients JavaScript

Par défaut, les clients JavaScript font référence aux méthodes de concentrateur à l’aide d’une version de casse mixte du nom de la méthode. SignalR effectue automatiquement cette modification afin que le code JavaScript peut être conforme aux conventions de JavaScript.

**Server**

[!code-csharp[Main](hubs-api-guide-server/samples/sample15.cs?highlight=1)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample16.js?highlight=1)]

Si vous souhaitez spécifier un autre nom pour les clients, ajoutez le `HubMethodName` attribut.

**Server**

[!code-csharp[Main](hubs-api-guide-server/samples/sample17.cs?highlight=1)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample18.js?highlight=1)]

<a id="asyncmethods"></a>

### <a name="when-to-execute-asynchronously"></a>Quand exécuter de façon asynchrone

Si la méthode sera être longue ou effectuer le travail qui serait impliquent en attente, telles que la recherche d’une base de données ou un appel de service web, la méthode de concentrateur asynchrone en retournant un [tâche](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) (à la place de `void` retourner) ou [ Tâche&lt;T&gt; ](https://msdn.microsoft.com/library/dd321424.aspx) objet (à la place de `T` type de retour). Lorsque vous retournez un `Task` objet à partir de la méthode SignalR attend le `Task` se termine, puis il transmet le résultat désencapsulé au client, il n’existe aucune différence dans la façon dont vous codez l’appel de méthode dans le client.

Effectue une méthode de concentrateur asynchrone évite de bloquer la connexion lorsqu’il utilise le transport WebSocket. Lorsqu’une méthode de concentrateur exécute de façon synchrone et le transport est WebSocket, les appels suivants de méthodes sur le concentrateur du même client sont bloquées jusqu'à la fin de la méthode de concentrateur.

L’exemple suivant montre la même méthode codé pour s’exécuter de façon synchrone ou asynchrone, suivi par le code JavaScript client qui fonctionne pour appeler des versions.

**Synchronous**

[!code-csharp[Main](hubs-api-guide-server/samples/sample19.cs)]

**Asynchronous**

[!code-csharp[Main](hubs-api-guide-server/samples/sample20.cs?highlight=1,7-8)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample21.js)]

Pour plus d’informations sur l’utilisation de méthodes asynchrones dans ASP.NET 4.5, consultez [à l’aide de méthodes asynchrones dans ASP.NET MVC 4](../../../mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4.md).

<a id="overloads"></a>

### <a name="defining-overloads"></a>Définir des surcharges

Si vous souhaitez définir de surcharges d’une méthode, le nombre de paramètres dans chaque surcharge doit être différent. Si vous différencier une surcharge simplement en spécifiant les types de paramètres différents, votre classe de concentrateur est compilés mais le service SignalR lève une exception au moment de l’exécution lorsque les clients essaient pour appeler l’une des surcharges.

<a id="progress"></a>
### <a name="reporting-progress-from-hub-method-invocations"></a>Signaler la progression à partir des appels de méthode de concentrateur

SignalR 2.1 prend en charge la [modèle de rapport de progression](https://blogs.msdn.com/b/dotnet/archive/2012/06/06/async-in-4-5-enabling-progress-and-cancellation-in-async-apis.aspx) introduite dans .NET 4.5. Pour implémenter le rapport de progression, définissez un `IProgress<T>` paramètre pour la méthode de concentrateur qui permettre accéder à votre client :

[!code-csharp[Main](hubs-api-guide-server/samples/sample22.cs)]

Lorsque vous écrivez une méthode de serveur longue, il est important d’utiliser un modèle de programmation asynchrone comme Async / Await plutôt qu’un blocage du thread de concentrateur.

<a id="callfromhub"></a>

## <a name="how-to-call-client-methods-from-the-hub-class"></a>Comment appeler des méthodes de client à partir de la classe de concentrateur

Pour appeler des méthodes de client à partir du serveur, utilisez le `Clients` propriété dans une méthode dans votre classe de concentrateur. L’exemple suivant montre le code de serveur qui appelle `addNewMessageToPage` sur tous les clients connectés et le code client qui définit la méthode dans un client JavaScript.

**Server**

[!code-csharp[Main](hubs-api-guide-server/samples/sample23.cs?highlight=5)]

**Client JavaScript à l’aide du proxy généré**

[!code-html[Main](hubs-api-guide-server/samples/sample24.html?highlight=1)]

Impossible d’obtenir une valeur de retour à partir d’une méthode du client ; la syntaxe `int x = Clients.All.add(1,1)` ne fonctionne pas.

Vous pouvez spécifier les types complexes et des tableaux pour les paramètres. L’exemple suivant passe un type complexe au client dans un paramètre de méthode.

**Code de serveur qui appelle une méthode du client à l’aide d’un objet complexe**

[!code-csharp[Main](hubs-api-guide-server/samples/sample25.cs?highlight=3)]

**Code de serveur qui définit l’objet complexe**

[!code-csharp[Main](hubs-api-guide-server/samples/sample26.cs?highlight=1)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample27.js?highlight=2-3)]

<a id="selectingclients"></a>

### <a name="selecting-which-clients-will-receive-the-rpc"></a>En sélectionnant les clients qui reçoivent l’appel RPC

La propriété retourne de Clients un [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx) objet qui fournit plusieurs options permettant de spécifier les clients qui recevront le RPC :

- Tous les clients connectés.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample28.cs)]
- Client appelant.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample29.cs)]
- Tous les clients sauf des clients.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample30.cs)]
- Un client spécifique identifié par l’ID de connexion.

    [!code-css[Main](hubs-api-guide-server/samples/sample31.css)]

    Cet exemple appelle `addContosoChatMessageToPage` sur le client appelant et a le même effet que l’utilisation de `Clients.Caller`.
- Tous les clients connectés à l’exception des clients spécifiés, identifiés par l’ID de connexion.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample32.cs)]
- Tous les clients connectés dans un groupe spécifié.

    [!code-css[Main](hubs-api-guide-server/samples/sample33.css)]
- Tous les clients connectés dans un groupe spécifié, à l’exception des clients spécifiés, identifié par l’ID de connexion.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample34.cs)]
- Tous les clients connectés dans un groupe spécifié à l’exception du client appelant.

    [!code-css[Main](hubs-api-guide-server/samples/sample35.css)]
- Un utilisateur spécifique, identifié par l’ID d’utilisateur.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample36.cs)]

    Par défaut, il s’agit de `IPrincipal.Identity.Name`, mais cela peut être modifié par [l’inscription d’une implémentation de IUserIdProvider avec l’hôte global](mapping-users-to-connections.md#IUserIdProvider).
- Tous les clients et les groupes dans une liste des ID de connexion.

    [!code-css[Main](hubs-api-guide-server/samples/sample37.css)]
- Une liste de groupes.

    [!code-css[Main](hubs-api-guide-server/samples/sample38.css)]
- Un utilisateur par nom.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample39.cs)]
- Liste des noms d’utilisateur (introduite dans SignalR 2.1).

    [!code-csharp[Main](hubs-api-guide-server/samples/sample40.cs)]

<a id="dynamicmethodnames"></a>

### <a name="no-compile-time-validation-for-method-names"></a>Aucune validation de la compilation pour les noms de méthode

Le nom de la méthode que vous spécifiez est interprété comme un objet dynamique, ce qui signifie qu'aucune IntelliSense ou la validation lors de la compilation. L’expression est évaluée au moment de l’exécution. Lorsque l’appel de méthode s’exécute, SignalR envoie le nom de méthode et les valeurs de paramètre au client, et si le client possède une méthode qui correspond au nom que la méthode est appelée et les valeurs de paramètre sont passés à ce dernier. Si aucune méthode correspondante n’est trouvée sur le client, aucune erreur n’est déclenché. Pour plus d’informations sur le format des données SignalR transmet au client en arrière-plan lorsque vous appelez une méthode du client, consultez [Introduction à SignalR](../getting-started/introduction-to-signalr.md).

<a id="caseinsensitive"></a>

### <a name="case-insensitive-method-name-matching"></a>Une correspondance de nom sans respecter la casse (méthode)

Correspondance de nom de méthode respecte la casse. Par exemple, `Clients.All.addContosoChatMessageToPage` sur le serveur s’exécute `AddContosoChatMessageToPage`, `addcontosochatmessagetopage`, ou `addContosoChatMessageToPage` sur le client.

<a id="asyncclient"></a>

### <a name="asynchronous-execution"></a>Exécution asynchrone

La méthode que vous appelez s’exécute de façon asynchrone. Tout code qui vient après qu’un appel de méthode à un client s’exécute immédiatement sans attendre que SignalR terminer la transmission de données aux clients, sauf si vous spécifiez que les lignes suivantes du code doivent attendre pour l’exécution de la méthode. L’exemple de code suivant montre comment exécuter deux méthodes de client de façon séquentielle.

**À l’aide d’Await (.NET 4.5)**

[!code-csharp[Main](hubs-api-guide-server/samples/sample41.cs?highlight=1,3)]

Si vous utilisez `await` pour attendre qu’une méthode de client se termine avant l’exécution de la ligne de code suivante, qui ne signifie pas que que les clients réellement le message avant l’exécution de la ligne de code suivante. « Exécution » d’un appel de méthode client signifie uniquement que SignalR a effectué toutes les opérations nécessaires pour envoyer le message. Si vous avez besoin que les clients ont reçu le message de vérification, vous devez programmer ce mécanisme. Par exemple, vous pourriez coder un `MessageReceived` méthode du concentrateur et dans le `addContosoChatMessageToPage` méthode sur le client, vous pouvez appeler `MessageReceived` après l’installation quelle que soit opérationnelle, vous devez effectuer sur le client. Dans `MessageReceived` dans le concentrateur, vous pouvez effectuer le travail dépend de réception du client réel et le traitement de l’appel de méthode d’origine.

### <a name="how-to-use-a-string-variable-as-the-method-name"></a>Comment utiliser une variable de chaîne en tant que nom de la méthode

Si vous souhaitez appeler une méthode du client à l’aide d’une variable de chaîne en tant que nom de la méthode, casté `Clients.All` (ou `Clients.Others`, `Clients.Caller`, etc.) à `IClientProxy` , puis appelez [Invoke (methodName, args...) ](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.iclientproxy.invoke(v=vs.111).aspx).

[!code-csharp[Main](hubs-api-guide-server/samples/sample42.cs)]

<a id="groupsfromhub"></a>

## <a name="how-to-manage-group-membership-from-the-hub-class"></a>Comment gérer l’appartenance au groupe à partir de la classe de concentrateur

Groupes dans SignalR fournissent une méthode pour diffuser des messages à des sous-ensembles spécifiés de clients connectés. Un groupe peut avoir n’importe quel nombre de clients, et un client peut être un membre de n’importe quel nombre de groupes.

Pour gérer l’appartenance au groupe, utilisez le [ajouter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.add(v=vs.111).aspx) et [supprimer](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.igroupmanager.remove(v=vs.111).aspx) méthodes fournies par le `Groups` propriété de la classe de concentrateur. L’exemple suivant illustre la `Groups.Add` et `Groups.Remove` méthodes utilisées dans les méthodes de concentrateur qui sont appelées par le code client, suivi par le code JavaScript client qui les appelle.

**Server**

[!code-csharp[Main](hubs-api-guide-server/samples/sample43.cs?highlight=5,10)]

**Client JavaScript à l’aide du proxy généré**

[!code-javascript[Main](hubs-api-guide-server/samples/sample44.js)]

[!code-javascript[Main](hubs-api-guide-server/samples/sample45.js)]

Vous n’êtes pas obligé de créer explicitement des groupes. En effet un groupe est créé automatiquement la première fois que vous spécifiez son nom dans un appel à `Groups.Add`, et il est supprimé lorsque vous supprimez la dernière connexion de l’appartenance à elle.

Il n’existe aucune API pour obtenir une liste de l’appartenance au groupe ou une liste de groupes. SignalR envoie des messages pour les clients et les groupes basés sur un [modèle de pub/sub](http://en.wikipedia.org/wiki/Publish/subscribe), et le serveur ne conserve pas les listes de groupes ou des appartenances aux groupes. Cela permet d’optimiser l’évolutivité, car chaque fois que vous ajoutez un nœud à une batterie de serveurs web, tout état SignalR tient à jour doit être propagée vers le nouveau nœud.

<a id="asyncgroupmethods"></a>

### <a name="asynchronous-execution-of-add-and-remove-methods"></a>Exécution asynchrone de méthodes Add et Remove

Le `Groups.Add` et `Groups.Remove` méthodes exécutent de façon asynchrone. Si vous souhaitez ajouter un client à un groupe et d’envoyer immédiatement un message au client en utilisant le groupe, vous devez vous assurer que le `Groups.Add` méthode se termine en premier. L’exemple de code suivant montre comment procéder.

**Ajout d’un client à un groupe et que le client de messagerie puis**

[!code-csharp[Main](hubs-api-guide-server/samples/sample46.cs?highlight=1,3)]

<a id="grouppersistence"></a>

### <a name="group-membership-persistence"></a>Persistance de l’appartenance au groupe

SignalR effectue le suivi des connexions, pas les utilisateurs, par conséquent, si vous souhaitez qu’un utilisateur soit dans le même groupe chaque fois que l’utilisateur établit une connexion, vous devez appeler `Groups.Add` chaque fois que l’utilisateur établit une nouvelle connexion.

Après une perte de connectivité temporaire, parfois SignalR peut restaurer la connexion automatiquement. Dans ce cas, SignalR restaure la même connexion, ne pas l’établissement d’une nouvelle connexion, et par conséquent, les appartenances de groupe du client est automatiquement restauré. Cela est possible même lorsque l’arrêt temporaire est le résultat d’un redémarrage du serveur ou l’échec, car l’état de connexion pour chaque client, y compris les appartenances de groupe, est un aller-retour vers le client. Si un serveur tombe en panne et est remplacé par un nouveau serveur avant l’expiration de la connexion, un client peut se reconnecter automatiquement au nouveau serveur et réinscrire dans des groupes, de qu'il est membre.

Lorsqu’une connexion ne peut pas être restaurée automatiquement après une perte de connectivité, ou lorsque la connexion arrive à expiration, ou lorsque le client se déconnecte (par exemple, lorsqu’un navigateur accède à une nouvelle page), les appartenances aux groupes sont perdues. La prochaine fois que l’utilisateur se connecte sera une nouvelle connexion. Pour gérer l’appartenance au groupe lorsque le même utilisateur établit une nouvelle connexion, votre application doit suivre les associations entre les utilisateurs et groupes et restaurer les appartenances aux groupes chaque fois qu’un utilisateur établit une nouvelle connexion.

Pour plus d’informations sur les connexions et les reconnexions, consultez [comment gérer les événements de durée de vie de connexion dans la classe de concentrateur](#connectionlifetime) plus loin dans cette rubrique.

<a id="singleusergroups"></a>

### <a name="single-user-groups"></a>Groupes d’utilisateur unique

Applications qui utilisent généralement des SignalR doivent effectuer le suivi des associations entre les utilisateurs et connexions afin de déterminer l’utilisateur qui a envoyé un message et les utilisateurs doivent recevoir un message. Pour cela, les groupes sont utilisés dans un des deux modèles couramment utilisés.

- Groupes d’utilisateur unique.

    Vous pouvez spécifier le nom d’utilisateur en tant que le nom du groupe et ajoutez l’ID de connexion actuel au groupe chaque fois que l’utilisateur se connecte ou se reconnecte. Pour envoyer des messages à l’utilisateur que vous envoyez au groupe. L’inconvénient de cette méthode est que le groupe ne vous fournit un moyen de savoir si l’utilisateur est en ligne ou hors connexion.
- Effectuer le suivi des associations entre les noms d’utilisateur et ID de connexion.

    Vous pouvez stocker une association entre chaque nom d’utilisateur et un ou plusieurs ID de connexion dans un dictionnaire ou d’une base de données et mettre à jour les données stockées chaque fois que l’utilisateur se connecte ou se déconnecte. Pour envoyer des messages à l’utilisateur, vous spécifiez l’ID de connexion. L’inconvénient de cette méthode est qu’il faut plus de mémoire.

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events-in-the-hub-class"></a>Comment gérer les événements de durée de vie de connexion dans la classe de concentrateur

Les raisons courantes pour la gestion des événements de durée de vie de connexion sont de savoir si un utilisateur est connecté ou non et pour effectuer le suivi de l’association entre les noms d’utilisateur et ID de connexion. Pour exécuter votre propre code lorsque les clients se connecteront ou déconnectent, remplacer le `OnConnected`, `OnDisconnected`, et `OnReconnected` méthodes virtuelles du concentrateur de classe, comme indiqué dans l’exemple suivant.

[!code-csharp[Main](hubs-api-guide-server/samples/sample47.cs?highlight=3,14,22)]

<a id="onreconnected"></a>

### <a name="when-onconnected-ondisconnected-and-onreconnected-are-called"></a>Lorsque les OnConnected, OnDisconnected et OnReconnected sont appelées

Chaque fois qu’un navigateur accède à une nouvelle page, une nouvelle connexion doit être établie, ce qui signifie que SignalR exécutera la `OnDisconnected` méthode suivie par le `OnConnected` (méthode). SignalR crée toujours un nouvel ID de connexion lorsqu’une nouvelle connexion est établie.

Le `OnReconnected` méthode est appelée en cas d’un arrêt temporaire de connectivité SignalR peut récupérer automatiquement à partir, par exemple lorsqu’un câble est temporairement déconnecté et reconnecté avant l’expiration de la connexion. Le `OnDisconnected` méthode est appelée lorsque le client est déconnecté et SignalR ne peut pas se reconnecter automatiquement, par exemple quand un navigateur accède à une nouvelle page. Par conséquent, une séquence possible d’événements pour un client donné est `OnConnected`, `OnReconnected`, `OnDisconnected`; ou `OnConnected`, `OnDisconnected`. Vous ne voyez pas la séquence `OnConnected`, `OnDisconnected`, `OnReconnected` pour une connexion donnée.

Le `OnDisconnected` méthode n’est pas appelée dans certains scénarios, tels que lorsqu’un serveur tombe en panne ou le domaine d’application obtient recyclé. Lorsqu’un autre serveur est en ligne ou le domaine d’application se termine son recyclage, certains clients peuvent être en mesure de se reconnecter et de déclencher la `OnReconnected` événement.

Pour plus d’informations, consultez [compréhension et gestion des événements de durée de vie de connexion dans SignalR](handling-connection-lifetime-events.md).

<a id="nocallerstate"></a>

### <a name="caller-state-not-populated"></a>État de l’appelant ne pas remplie

Les méthodes de gestionnaire de connexions durée de vie des événements sont appelés à partir du serveur, ce qui signifie que les États que vous placez dans le `state` objet sur le client ne sera pas renseigné dans le `Caller` propriété sur le serveur. Pour plus d’informations sur la `state` objet et la `Caller` propriété, consultez [comment passer l’état entre les clients et de la classe de concentrateur](#passstate) plus loin dans cette rubrique.

<a id="contextproperty"></a>

## <a name="how-to-get-information-about-the-client-from-the-context-property"></a>Comment obtenir des informations sur le client à partir de la propriété de contexte

Pour obtenir plus d’informations sur le client, utilisez le `Context` propriété de la classe de concentrateur. Le `Context` propriété retourne un [HubCallerContext](https://msdn.microsoft.com/library/jj890883(v=vs.111).aspx) objet qui fournit l’accès aux informations suivantes :

- L’ID de connexion du client appelant.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample48.cs?highlight=1)]

    L’ID de connexion est un GUID qui est affecté par SignalR (vous ne pouvez pas spécifier la valeur dans votre propre code). Il existe un ID de connexion pour chaque connexion et le même QU'ID est utilisé par tous les concentrateurs si vous avez plusieurs concentrateurs dans votre application.
- Données d’en-tête HTTP.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample49.cs?highlight=1)]

    Vous pouvez également obtenir des en-têtes HTTP à partir de `Context.Headers`. La raison de plusieurs références à la même chose est que `Context.Headers` a été créé en premier lieu, le `Context.Request` propriété a été ajoutée à une version ultérieure, et `Context.Headers` a été conservée pour compatibilité descendante.
- Données de chaîne de requête.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample50.cs?highlight=1)]

    Vous pouvez également obtenir des données de chaîne de requête `Context.QueryString`.

    La chaîne de requête que vous obtenez dans cette propriété est celui qui a été utilisé avec la requête HTTP qui a établi la connexion de SignalR. Vous pouvez ajouter des paramètres de chaîne de requête dans le client en configurant la connexion, ce qui constitue un moyen pratique pour passer des données sur le client à partir du client au serveur. L’exemple suivant montre une façon d’ajouter une chaîne de requête dans un client JavaScript lorsque vous utilisez le proxy généré.

    [!code-javascript[Main](hubs-api-guide-server/samples/sample51.js?highlight=1)]

    Pour plus d’informations sur la définition des paramètres de chaîne de requête, consultez les guides d’API pour le [JavaScript](hubs-api-guide-javascript-client.md) et [.NET](hubs-api-guide-net-client.md) les clients.

    Vous pouvez trouver le mode de transport utilisé pour la connexion dans les données de chaîne de requête, ainsi que certaines autres valeurs utilisées en interne par SignalR :

    [!code-csharp[Main](hubs-api-guide-server/samples/sample52.cs)]

    La valeur de `transportMethod` sera « webSockets », « serverSentEvents », « foreverFrame » ou « longPolling ». Notez que si vous vérifiez cette valeur la `OnConnected` méthode de gestionnaire d’événements, dans certains scénarios, vous pouvez initialement obtenir une valeur de transport qui n’est pas la méthode de transport négociée final pour la connexion. Dans ce cas, la méthode lève une exception et est appelée plus tard lorsque le mode de transport finale est établi.
- Cookies.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample53.cs?highlight=1)]

    Vous pouvez également obtenir les cookies à partir de `Context.RequestCookies`.
- Informations sur l’utilisateur.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample54.cs?highlight=1)]
- L’objet HttpContext pour la demande :

    [!code-csharp[Main](hubs-api-guide-server/samples/sample55.cs?highlight=1)]

    Utilisez cette méthode au lieu d’obtenir `HttpContext.Current` pour obtenir le `HttpContext` objet pour la connexion de SignalR.

<a id="passstate"></a>

## <a name="how-to-pass-state-between-clients-and-the-hub-class"></a>Comment passer l’état entre les clients et de la classe de concentrateur

Le proxy client fournit un `state` objet dans lequel vous pouvez stocker les données que vous souhaitez transmettre au serveur avec chaque appel de méthode. Sur le serveur, vous pouvez accéder à ces données dans le `Clients.Caller` propriété dans les méthodes de concentrateur qui sont appelées par les clients. Le `Clients.Caller` propriété n’est pas remplie pour les méthodes du Gestionnaire d’événements connexion durée de vie `OnConnected`, `OnDisconnected`, et `OnReconnected`.

Création ou mise à jour des données dans le `state` objet et le `Clients.Caller` propriété fonctionne dans les deux sens. Vous pouvez mettre à jour les valeurs dans le serveur et qu’ils sont transmis au client.

L’exemple suivant montre le code JavaScript client qui stocke l’état de transmission au serveur à chaque appel de méthode.

[!code-javascript[Main](hubs-api-guide-server/samples/sample56.js?highlight=1-2)]

L’exemple suivant montre le code équivalent dans un client .NET.

[!code-csharp[Main](hubs-api-guide-server/samples/sample57.cs?highlight=1-2)]

Dans votre classe de concentrateur, vous pouvez accéder à ces données dans le `Clients.Caller` propriété. L’exemple suivant montre le code qui Récupère l’état dans l’exemple précédent.

[!code-csharp[Main](hubs-api-guide-server/samples/sample58.cs?highlight=3-4)]

> [!NOTE]
> Ce mécanisme pour rendre persistant l’état n’est pas destiné pour de grandes quantités de données, depuis tout ce dont vous placer dans le `state` ou `Clients.Caller` propriété est un aller-retour avec chaque appel de méthode. Il est utile pour les éléments plus petits, tels que les noms d’utilisateur ou de compteurs.


Dans VB.NET ou dans un concentrateur fortement typée, l’objet d’état de l’appelant ne peut pas être accessibles via `Clients.Caller`; au lieu de cela, utilisez `Clients.CallerState` (introduite dans SignalR 2.1) :

**À l’aide de CallerState en c#**

[!code-csharp[Main](hubs-api-guide-server/samples/sample59.cs?highlight=3-4)]

**À l’aide de CallerState en Visual Basic**

[!code-vb[Main](hubs-api-guide-server/samples/sample60.vb)]

<a id="handleErrors"></a>

## <a name="how-to-handle-errors-in-the-hub-class"></a>Comment gérer les erreurs dans la classe de concentrateur

Pour gérer les erreurs qui se produisent dans vos méthodes de classe de concentrateur, utilisez une ou plusieurs des méthodes suivantes :

- Encapsuler votre code de la méthode dans les blocs try-catch et les journaux de l’objet exception. À des fins de débogage, vous pouvez envoyer l’exception au client, mais pour la sécurité de raisons d’envoyer des informations détaillées sur les clients en production ne sont pas recommandées.
- Créer un module de pipeline concentrateurs qui gère la [OnIncomingError](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubpipelinemodule.onincomingerror(v=vs.111).aspx) (méthode). L’exemple suivant montre un module de pipeline qui enregistre les erreurs, suivis par le code dans Startup.cs qui injecte le module dans le pipeline de concentrateurs.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample61.cs)]

    [!code-csharp[Main](hubs-api-guide-server/samples/sample62.cs?highlight=4)]
- Utilisez la `HubException` classe (introduite dans SignalR 2). Cette erreur peut être levée à partir de n’importe quel appel de concentrateur. Le `HubError` le constructeur prend un message de chaîne et un objet pour le stockage des données d’erreur supplémentaires. SignalR auto-sérialiser l’exception et l’envoyer au client, où il sera utilisé pour refuser ou de faire échouer l’appel de méthode de concentrateur.

    Les exemples de code suivants montrent comment lever une `HubException` pendant un appel de concentrateur et comment gérer l’exception sur les clients JavaScript et .NET.

    **Code de serveur illustrant la classe HubException**

    [!code-csharp[Main](hubs-api-guide-server/samples/sample63.cs)]

    **Code JavaScript client qui montre la réponse à la levée d’une HubException dans un concentrateur**

    [!code-html[Main](hubs-api-guide-server/samples/sample64.html)]

    **Code client .NET qui montre la réponse à la levée d’une HubException dans un concentrateur**

    [!code-csharp[Main](hubs-api-guide-server/samples/sample65.cs)]

Pour plus d’informations sur les modules du pipeline Hub, consultez [comment personnaliser le pipeline de concentrateurs](#hubpipeline) plus loin dans cette rubrique.

<a id="tracing"></a>

## <a name="how-to-enable-tracing"></a>Comment activer le suivi

Pour activer le suivi du côté serveur, ajouter un élément system.diagnostics à votre fichier Web.config, comme indiqué dans cet exemple :

[!code-html[Main](hubs-api-guide-server/samples/sample66.html?highlight=17-72)]

Lorsque vous exécutez l’application dans Visual Studio, vous pouvez afficher les journaux dans le **sortie** fenêtre.

<a id="callfromoutsidehub"></a>

## <a name="how-to-call-client-methods-and-manage-groups-from-outside-the-hub-class"></a>Comment appeler des méthodes de client et de gérer des groupes à l’extérieur de la classe de concentrateur

Pour appeler des méthodes de client à partir d’une autre classe que votre classe de concentrateur, obtenir une référence à l’objet de contexte SignalR pour le concentrateur et l’utiliser pour appeler des méthodes sur le client ou gérer des groupes.

L’exemple suivant `StockTicker` classe obtient l’objet de contexte, il stocke dans une instance de la classe, stocke l’instance de classe dans une propriété statique et utilise le contexte de l’instance de la classe singleton pour appeler le `updateStockPrice` sur les clients (méthode) connecté à un concentrateur nommé `StockTickerHub`.

[!code-csharp[Main](hubs-api-guide-server/samples/sample67.cs?highlight=8,24)]

Si vous avez besoin d’utiliser les contexte plusieurs fois dans un objet de longue durée, obtenir la référence une seule fois et enregistrez il plutôt que de l’obtenir chaque fois. Obtenir le contexte qu’une seule fois garantit que SignalR envoie des messages aux clients dans l’ordre dans lequel vos méthodes de concentrateur rendre client appels de méthode. Pour obtenir un didacticiel qui montre comment utiliser le contexte de SignalR pour un concentrateur, consultez [serveur de diffusion avec ASP.NET SignalR](../getting-started/tutorial-server-broadcast-with-signalr.md).

<a id="callingclientsoutsidehub"></a>

### <a name="calling-client-methods"></a>Appel de méthodes de client

Vous pouvez spécifier les clients qui recevront le RPC, mais vous avez moins d’options que lorsque vous appelez à partir d’une classe de concentrateur. La raison en est que le contexte n’est pas associé à un appel particulier à partir d’un client, donc toutes les méthodes qui requièrent des connaissances de l’ID de connexion en cours, telles que `Clients.Others`, ou `Clients.Caller`, ou `Clients.OthersInGroup`, ne sont pas disponibles. Les options ci-dessous sont disponibles :

- Tous les clients connectés.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample68.cs)]
- Un client spécifique identifié par l’ID de connexion.

    [!code-css[Main](hubs-api-guide-server/samples/sample69.css)]
- Tous les clients connectés à l’exception des clients spécifiés, identifiés par l’ID de connexion.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample70.cs)]
- Tous les clients connectés dans un groupe spécifié.

    [!code-css[Main](hubs-api-guide-server/samples/sample71.css)]
- Tous les clients connectés dans un groupe spécifié, à l’exception des clients spécifiés, identifié par l’ID de connexion.

    [!code-csharp[Main](hubs-api-guide-server/samples/sample72.cs)]

Si vous appelez dans votre classe non-Hub à partir des méthodes dans votre classe de concentrateur, vous pouvez entrer l’ID de connexion en cours et l’utiliser avec `Clients.Client`, `Clients.AllExcept`, ou `Clients.Group` pour simuler `Clients.Caller`, `Clients.Others`, ou `Clients.OthersInGroup`. Dans l’exemple suivant, la `MoveShapeHub` classe passe l’ID de connexion à la `Broadcaster` classe afin que la `Broadcaster` classe capable de simuler `Clients.Others`.

[!code-csharp[Main](hubs-api-guide-server/samples/sample73.cs?highlight=12,36)]

<a id="managinggroupsoutsidehub"></a>

### <a name="managing-group-membership"></a>La gestion de l’appartenance au groupe

Pour la gestion des groupes, vous avez les mêmes options comme vous le feriez dans une classe de concentrateur.

- Ajouter un client à un groupe

    [!code-csharp[Main](hubs-api-guide-server/samples/sample74.cs)]
- Supprimer un client à partir d’un groupe

    [!code-css[Main](hubs-api-guide-server/samples/sample75.css)]

<a id="hubpipeline"></a>

## <a name="how-to-customize-the-hubs-pipeline"></a>Comment personnaliser le pipeline de concentrateurs

SignalR vous permet d’injecter votre propre code dans le pipeline de concentrateur. L’exemple suivant montre un module de pipeline de concentrateur personnalisé qui enregistre chaque appel de méthode entrant reçu du client et l’appel de méthode sortant est appelée sur le client :

[!code-csharp[Main](hubs-api-guide-server/samples/sample76.cs)]

Le code suivant dans le *Startup.cs* fichier enregistre le module à s’exécuter dans le pipeline de Hub :

[!code-csharp[Main](hubs-api-guide-server/samples/sample77.cs?highlight=3)]

Il existe de nombreuses méthodes différentes que vous pouvez substituer. Pour obtenir la liste complète, consultez [HubPipelineModule méthodes](https://msdn.microsoft.com/library/jj918633(v=vs.111).aspx).

---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: "Guide d’API ASP.NET SignalR concentrateurs - Client JavaScript | Documents Microsoft"
author: pfletcher
description: "Ce document fournit une introduction à l’utilisation de l’API de Hubs pour SignalR version 2, les clients JavaScript, telles que les navigateurs et en cours du Windows Store (WinJS)..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/28/2015
ms.topic: article
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 65e369a393a8c5d2d1bba11b5c71347df8f9c69d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a>Guide d’API ASP.NET SignalR concentrateurs - Client JavaScript
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)

> Ce document fournit une introduction à l’utilisation de l’API de Hubs pour SignalR version 2, les clients JavaScript, telles que des navigateurs et des applications du Windows Store (WinJS).
> 
> L’API de concentrateurs SignalR vous permet de vous permettent d’effectuer des appels de procédure distante (RPC) à partir d’un serveur pour les clients connectés et à partir de clients sur le serveur. Dans le code serveur, vous définissez les méthodes qui peuvent être appelées par les clients, et vous appelez des méthodes qui s’exécutent sur le client. Dans le code client, vous définissez les méthodes qui peuvent être appelées à partir du serveur, et vous appelez des méthodes qui s’exécutent sur le serveur. SignalR prend en charge de tous les éléments client-serveur pour vous.
> 
> SignalR offre également une API de niveau inférieur appelée connexions persistantes. Pour obtenir une présentation SignalR, des concentrateurs et des connexions persistantes, consultez [Introduction à SignalR](../getting-started/introduction-to-signalr.md).
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

- [Le proxy généré et ce qu’il fait pour vous](#genproxy)

    - [Quand utiliser le proxy généré](#cantusegenproxy)
- [Programme d’installation du client](#clientsetup)

    - [Comment référencer le proxy généré de manière dynamique](#dynamicproxy)
    - [Comment créer un fichier physique pour SignalR de proxy généré](#manualproxy)
- [Comment établir une connexion](#establishconnection)

    - [$. connection.hub est le même que $.hubConnection() crée l’objet](#connequivalence)
    - [Exécution asynchrone de la méthode de démarrage](#asyncstart)
- [Comment établir une connexion entre domaines](#crossdomain)
- [Comment configurer la connexion](#configureconnection)

    - [Comment spécifier des paramètres de chaîne de requête](#querystring)
    - [Comment spécifier le mode de transport](#transport)
- [Comment obtenir un proxy pour une classe de concentrateur](#getproxy)
- [Comment définir des méthodes sur le client que le serveur peut appeler.](#callclient)
- [Comment appeler des méthodes de serveur à partir du client](#callserver)
- [Comment gérer les événements de durée de vie de connexion](#connectionlifetime)
- [Comment gérer les erreurs](#handleerrors)
- [Comment activer la journalisation côté client](#logging)

Pour plus d’informations sur la façon de programmer le serveur ou les clients .NET, consultez les ressources suivantes :

- [Guide d’API concentrateurs SignalR - serveur](hubs-api-guide-server.md)
- [Guide d’API concentrateurs SignalR - Client .NET](hubs-api-guide-net-client.md)

Le composant de serveur SignalR 2 est uniquement disponible sur .NET 4.5 (s’il existe un client .NET pour 2 SignalR sur .NET 4.0).

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a>Le proxy généré et ce qu’il fait pour vous

Vous pouvez programmer un client JavaScript pour communiquer avec un service SignalR avec ou sans un proxy SignalR génère pour vous. Ce que fait le serveur proxy pour vous est de simplifier la syntaxe du code que vous utilisez pour vous connecter, les méthodes d’écriture que le serveur appelle, et appelez des méthodes sur le serveur.

Lorsque vous écrivez du code pour appeler des méthodes de serveur, le proxy généré vous permet d’utiliser la syntaxe qui semble que vous s’exécutaient une fonction locale : vous pouvez écrire `serverMethod(arg1, arg2)` au lieu de `invoke('serverMethod', arg1, arg2)`. La syntaxe de proxy généré permet également une erreur côté client immédiate et intelligible si vous orthographiez mal le nom d’une méthode de serveur. Et si vous créez manuellement le fichier qui définit les serveurs proxy, vous pouvez également obtenir de prise en charge IntelliSense pour l’écriture de code qui appelle des méthodes de serveur.

Par exemple, supposons que vous disposez de la classe de concentrateur suivante sur le serveur :

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

Les exemples de code suivants montrent ce qui se présente le code JavaScript, pour appeler le `NewContosoChatMessage` méthode sur le serveur et de recevoir des appels de la `addContosoChatMessageToPage` méthode à partir du serveur.

**Avec le proxy généré**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

**Sans le proxy généré**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a>Quand utiliser le proxy généré

Si vous souhaitez inscrire une méthode de client que le serveur appelle plusieurs gestionnaires d’événements, vous ne pouvez pas utiliser le proxy généré. Dans le cas contraire, vous pouvez choisir d’utiliser le proxy généré ou non en fonction de vos préférences de codage. Si vous choisissez de ne pas l’utiliser, vous n’êtes pas obligé de faire référence à l’URL « / concentrateurs signalr » dans un `script` élément dans votre code client.

<a id="clientsetup"></a>

## <a name="client-setup"></a>Programme d’installation du client

Un client JavaScript nécessite des références aux jQuery et le fichier JavaScript de base SignalR. La version de jQuery doit être 1.6.4 ou versions ultérieures principales, comme 1.7.2, 1.8.2 ou 1.9.1. Si vous décidez d’utiliser le proxy généré, vous devez également une référence au fichier JavaScript proxy SignalR généré. L’exemple suivant montre ce que les références peut se présenter comme dans une page HTML qui utilise le proxy généré.

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

Ces références doivent être inclus dans cet ordre : jQuery first, last, SignalR core après cela et les proxys de SignalR.

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a>Comment référencer le proxy généré de manière dynamique

Dans l’exemple précédent, la référence au proxy SignalR généré est au code JavaScript généré dynamiquement, pas à un fichier physique. SignalR crée le code JavaScript pour le proxy à la volée et le fournit au client en réponse à l’URL « concentrateurs signalr / / ». Si vous avez spécifié une autre URL de base pour les connexions SignalR sur le serveur dans votre `MapSignalR` , l’URL du fichier proxy généré dynamiquement est votre URL personnalisée avec « / concentrateurs » ajouté à la fin.

> [!NOTE]
> Pour les clients JavaScript de Windows 8 (Windows Store), utilisez le fichier de proxy physique au lieu de celle générée dynamiquement. Pour plus d’informations, consultez [proxy généré de la création d’un fichier physique pour SignalR](#manualproxy) plus loin dans cette rubrique.


Dans un ASP.NET MVC 4 ou 5 Razor, utilisez le tilde pour faire référence à la racine de l’application dans votre référence de fichier proxy :

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

Pour plus d’informations sur l’utilisation de SignalR dans MVC 5, consultez [prise en main SignalR et MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).

Dans une vue ASP.NET MVC 3 Razor, utilisez `Url.Content` pour des références de fichier proxy :

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

Dans une application ASP.NET Web Forms, utilisez `ResolveClientUrl` pour les proxys de référence de fichier ou enregistrez-le via ScriptManager à l’aide d’une application racine chemin d’accès relatif (commence par un tilde) :

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

En règle générale, utilisez la même méthode pour spécifier l’URL « concentrateurs signalr / / » que vous utilisez pour les fichiers CSS ou JavaScript. Si vous spécifiez une URL sans l’aide d’un tilde, dans certains scénarios de votre application fonctionnera correctement lorsque vous testez dans Visual Studio à l’aide d’IIS Express, mais échoue avec une erreur 404 lorsque vous déployez vers IIS complet. Pour plus d’informations, consultez **résolution des références aux ressources de niveau racine** dans [serveurs Web dans Visual Studio pour les projets Web ASP.NET](https://msdn.microsoft.com/en-us/library/58wxa9w5.aspx) sur le site MSDN.

Lorsque vous exécutez un projet web dans Visual Studio 2013 en mode débogage, et si vous utilisez Internet Explorer comme navigateur, vous pouvez voir le fichier proxy dans **l’Explorateur de solutions** sous **Documents de Script**, comme illustré dans le illustration suivante.

![Fichier proxy généré de JavaScript dans l’Explorateur de solutions](hubs-api-guide-javascript-client/_static/image1.png)

Pour afficher le contenu du fichier, double-cliquez sur **concentrateurs**. Si vous n’utilisez pas Visual Studio 2012 ou 2013 et Internet Explorer, ou si vous n’êtes pas en mode débogage, vous pouvez également obtenir le contenu du fichier en accédant à l’URL « concentrateurs signalR / / ». Par exemple, si votre site est en cours d’exécution à `http://localhost:56699`, accédez à `http://localhost:56699/SignalR/hubs` dans votre navigateur.

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a>Comment créer un fichier physique pour SignalR de proxy généré

Comme alternative au proxy généré de manière dynamique, vous pouvez créer un fichier physique qui a le code proxy et référencer ce fichier. Vous pourriez le faire pour contrôler la mise en cache ou le comportement de regroupement ou d’utiliser IntelliSense lorsque vous codez des appels aux méthodes de serveur.

Pour créer un fichier proxy, procédez comme suit :

1. Installer le [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) package NuGet.
2. Ouvrez une invite de commandes et accédez à la *outils* dossier qui contient le fichier SignalR.exe. Le dossier Outils est à l’emplacement suivant :

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. Entrez la commande suivante :

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    Le chemin d’accès à votre *.dll* est généralement le *bin* dans votre dossier de projet.

    Cette commande crée un fichier nommé *server.js* dans le même dossier que *signalr.exe*.
4. Placez le *server.js* de fichiers dans un dossier approprié à votre projet, renommez-la en fonction de votre application et ajouter une référence à celui-ci à la place de la référence « concentrateurs signalr / ».

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a>Comment établir une connexion

Avant de pouvoir établir une connexion, vous devez créer un objet de connexion, créer un proxy et inscrire les gestionnaires d’événements pour les méthodes qui peuvent être appelées à partir du serveur. Lorsque les gestionnaires d’événements et de proxy sont configurés, établir la connexion en appelant le `start` (méthode).

Si vous utilisez le proxy généré, il est inutile de créer l’objet de connexion dans votre propre code, car le code proxy généré le fait pour vous.

<a id="nogenconnection"></a>

**Établir une connexion (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

**Établir une connexion (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

L’exemple de code utilise la valeur par défaut « / signalr « URL pour se connecter à votre service de SignalR. Pour plus d’informations sur la façon de spécifier une autre URL de base, consultez [ASP.NET SignalR concentrateurs API Guide - Server - l’URL /signalr](hubs-api-guide-server.md#signalrurl).

Par défaut, l’emplacement du concentrateur est le serveur actuel ; Si vous vous connectez à un autre serveur, spécifiez l’URL avant d’appeler le `start` méthode, comme indiqué dans l’exemple suivant :

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> Normalement, vous inscrivez les gestionnaires d’événements avant d’appeler le `start` méthode pour établir la connexion. Si vous souhaitez enregistrer certains gestionnaires d’événements après avoir établi la connexion, vous pouvez le faire, mais vous devez vous inscrire au moins un de vos gestionnaires d’événements avant d’appeler le `start` (méthode). Une des raisons sont qu’il peut y avoir de nombreux concentrateurs dans une application, mais vous ne souhaitez pas déclencher le `OnConnected` événement sur chaque Hub si vous vous apprêtez uniquement à utiliser pour un d’eux. Lorsque la connexion est établie, la présence d’une méthode du client sur le proxy d’un concentrateur est ce qui indique à SignalR pour déclencher le `OnConnected` événement. Si vous n’inscrivez les gestionnaires d’événements avant d’appeler le `start` (méthode), vous serez en mesure d’appeler des méthodes sur le concentrateur, mais de concentrateur `OnConnected` méthode ne sera pas appelée et aucune méthode client ne sera appelée à partir du serveur.


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a>$. connection.hub est le même que $.hubConnection() crée l’objet

Comme vous pouvez voir des exemples, lorsque vous utilisez le proxy généré, `$.connection.hub` fait référence à l’objet de connexion. Il s’agit du même objet que vous obtenez en appelant `$.hubConnection()` lorsque vous n’utilisez pas le proxy généré. Le code proxy généré crée la connexion pour vous en exécutant l’instruction suivante :

![Création d’une connexion dans le fichier proxy généré](hubs-api-guide-javascript-client/_static/image3.png)

Lorsque vous utilisez le proxy généré, vous pouvez faire tout ce avec `$.connection.hub` que vous pouvez faire avec un objet de connexion lorsque vous n’utilisez pas le proxy généré.

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a>Exécution asynchrone de la méthode de démarrage

Le `start` méthode s’exécute de façon asynchrone. Elle retourne un [jQuery différé objet](http://api.jquery.com/category/deferred-object/), ce qui signifie que vous pouvez ajouter des fonctions de rappel en appelant des méthodes comme `pipe`, `done`, et `fail`. Si vous disposez du code que vous souhaitez exécuter une fois la connexion établie, tel qu’un appel à une méthode de serveur, placez ce code dans une fonction de rappel ou l’appeler à partir d’une fonction de rappel. Le `.done` méthode de rappel est exécutée après la connexion a été établie, et une fois que tout code que vous avez votre `OnConnected` fin de la méthode de gestionnaire d’événements sur le serveur de l’exécution.

Si vous placez l’instruction « Maintenant connecté » de l’exemple précédent en tant que la ligne suivante de code après le `start` appel de méthode (pas dans un `.done` rappel), le `console.log` ligne s’exécute avant que la connexion est établie, comme indiqué dans l’exemple suivant exemple :

![Mauvaise façon d’écrire du code qui s’exécute après que la connexion est établie.](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a>Comment établir une connexion entre domaines

En général, si le navigateur charge une page à partir de `http://contoso.com`, la connexion SignalR est dans le même domaine, `http://contoso.com/signalr`. Si la page à partir de `http://contoso.com` établit une connexion à `http://fabrikam.com/signalr`, qui est une connexion entre domaines. Pour des raisons de sécurité, les connexions inter-domaines sont désactivées par défaut.

SignalR 1.x, des requêtes entre domaines ont été contrôlé par un indicateur EnableCrossDomain unique. Cet indicateur contrôlé demandes JSONP et CORS. Pour plus de souplesse, toutes les règles CORS prennent en charge a été retiré le composant serveur du SignalR (clients JavaScript toujours utiliseront CORS normalement s’il est détecté que le navigateur prend en charge), et nouveau intergiciel (middleware) OWIN a été rendue disponible pour prendre en charge ces scénarios.

Si JSONP est requise sur le client (pour prendre en charge les demandes entre domaines dans les anciens navigateurs), il doit être activée explicitement en définissant `EnableJSONP` sur la `HubConfiguration` objet `true`, comme illustré ci-dessous. JSONP est désactivé par défaut, car il est moins sécurisée que CORS.

**Ajout de Microsoft.Owin.Cors à votre projet :** pour installer cette bibliothèque, exécutez la commande suivante dans la Console du Gestionnaire de Package :

`Install-Package Microsoft.Owin.Cors`

Cette commande ajoute le 2.1.0 version du package pour votre projet.

### <a name="calling-usecors"></a>Appel de UseCors

 L’extrait de code suivant montre comment implémenter des connexions inter-domaines dans SignalR 2. 

**Implémentation des demandes inter-domaines dans SignalR 2**

Le code suivant montre comment activer les CORS ou JSONP dans un projet SignalR 2. Cet exemple de code utilise `Map` et `RunSignalR` au lieu de `MapSignalR`, de sorte que l’intergiciel (middleware) CORS s’exécute uniquement pour les demandes de SignalR qui requièrent la prise en charge CORS (plutôt que pour tout le trafic sur le chemin d’accès spécifié dans `MapSignalR`.) Carte peut également servir pour n’importe quel autre intergiciel (middleware) qui doit s’exécuter pour un préfixe d’URL spécifique, plutôt que pour l’application entière.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE] 
> 
> - Ne définissez pas `jQuery.support.cors` à true dans votre code.
> 
>     ![Ne définissez pas jQuery.support.cors sur true](hubs-api-guide-javascript-client/_static/image7.png)
> 
>     SignalR gère l’utilisation de CORS. Paramètre `jQuery.support.cors` à true désactive JSONP car elle force SignalR à assumer le navigateur prend en charge CORS.
> - Lorsque vous vous connectez à une URL localhost, Internet Explorer 10 ne considèrent comme une connexion entre domaines, pour l’application fonctionne localement avec Internet Explorer 10 même si vous n’avez pas activé les connexions entre domaines sur le serveur.
> - Pour plus d’informations sur l’utilisation de connexions inter-domaines avec Internet Explorer 9, consultez [ce thread StackOverflow](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).
> - Pour plus d’informations sur l’utilisation de connexions inter-domaines avec Chrome, consultez [ce thread StackOverflow](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).
> - L’exemple de code utilise la valeur par défaut « / signalr « URL pour se connecter à votre service de SignalR. Pour plus d’informations sur la façon de spécifier une autre URL de base, consultez [ASP.NET SignalR concentrateurs API Guide - Server - l’URL /signalr](hubs-api-guide-server.md#signalrurl).


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a>Comment configurer la connexion

Avant d’établir une connexion, vous pouvez spécifier des paramètres de chaîne de requête ou spécifier le mode de transport.

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a>Comment spécifier des paramètres de chaîne de requête

Si vous souhaitez envoyer des données sur le serveur lorsque le client se connecte, vous pouvez ajouter des paramètres de chaîne de requête à l’objet de connexion. Les exemples suivants montrent comment définir un paramètre de chaîne de requête dans le code client.

**Définir une valeur de chaîne de requête avant d’appeler la méthode start (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

**Définir une valeur de chaîne de requête avant d’appeler la méthode start (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

L’exemple suivant montre comment lire un paramètre de chaîne de requête dans le code serveur.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a>Comment spécifier le mode de transport

Dans le cadre du processus de connexion, un client SignalR est normalement négocie avec le serveur pour déterminer le transport meilleure prise en charge par le serveur et client. Si vous savez déjà que vous souhaitez utiliser le transport, vous pouvez ignorer ce processus de négociation en spécifiant le mode de transport lorsque vous appelez le `start` (méthode).

**Code client qui spécifie le mode de transport (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

**Code client qui spécifie le mode de transport (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

En guise d’alternative, vous pouvez spécifier plusieurs méthodes de transport dans l’ordre dans lequel vous souhaitez SignalR à les essayer :

**Code client qui spécifie un schéma de secours de transport personnalisé (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

**Code client qui spécifie un schéma de secours de transport personnalisés (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

Vous pouvez utiliser les valeurs suivantes pour spécifier le mode de transport :

- « webSockets »
- « foreverFrame »
- « serverSentEvents »
- « longPolling »

Les exemples suivants montrent comment déterminer quelle méthode de transport est utilisé par une connexion.

**Code client qui affiche le mode de transport utilisé par une connexion (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

**Code client qui affiche le mode de transport utilisé par une connexion (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

Pour plus d’informations sur la vérification de la méthode de transport dans le code serveur, consultez [ASP.NET SignalR concentrateurs API Guide - Server - comment obtenir des informations sur le client à partir de la propriété de contexte](hubs-api-guide-server.md#contextproperty). Pour plus d’informations sur les transports et de secours, consultez [Introduction à SignalR - Transports et secours](../getting-started/introduction-to-signalr.md#transports).

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a>Comment obtenir un proxy pour une classe de concentrateur

Chaque objet de connexion que vous créez encapsule des informations sur une connexion à un service de SignalR qui contient une ou plusieurs classes de concentrateur. Pour communiquer avec une classe de concentrateur, vous utilisez un objet proxy que vous créez vous-même (si vous n’utilisez pas le proxy généré) ou qui est généré pour vous.

Sur le client le nom du proxy est une version de casse mixte du nom de classe du concentrateur. SignalR effectue automatiquement cette modification afin que le code JavaScript peut être conforme aux conventions de JavaScript.

**Classe de concentrateur sur le serveur**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

**Obtenir une référence au proxy client généré pour le concentrateur**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

**Créer le proxy client pour la classe de concentrateur (sans proxy généré)**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

Si vous ajoutez votre classe Hub avec un `HubName` d’attribut, utilisez le nom exact sans changement de casse.

**Classe de concentrateur sur le serveur avec l’attribut de HubName**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

**Obtenir une référence au proxy client généré pour le concentrateur**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

**Créer le proxy client pour la classe de concentrateur (sans proxy généré)**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a>Comment définir des méthodes sur le client que le serveur peut appeler.

Pour définir une méthode que le serveur peut appeler à partir d’un concentrateur, ajoutez un gestionnaire d’événements pour le concentrateur proxy à l’aide de la `client` propriété du proxy généré ou appel de la `on` méthode si vous n’utilisez pas le proxy généré. Les paramètres peuvent être des objets complexes.

Ajouter le Gestionnaire d’événements avant d’appeler le `start` méthode pour établir la connexion. (Si vous souhaitez ajouter des gestionnaires d’événements après avoir appelé la `start` (méthode), consultez la note de [comment établir une connexion](#establishconnection) plus haut dans ce document et utilisez la syntaxe indiquée pour la définition d’une méthode sans utiliser le proxy généré.)

Correspondance de nom de méthode respecte la casse. Par exemple, `Clients.All.addContosoChatMessageToPage` sur le serveur s’exécute `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, ou `addcontosochatmessagetopage` sur le client.

**Définir la méthode sur le client (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

**Autre manière de définir la méthode sur le client (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

**Définir la méthode sur le client (sans le proxy généré, ou lorsque vous ajoutez après avoir appelé la méthode de démarrage)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

**Code de serveur qui appelle la méthode du client**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

Les exemples suivants incluent un objet complexe comme un paramètre de méthode.

**Définir la méthode sur le client qui prend un objet complexe (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

**Définir la méthode sur le client qui prend un objet complexe (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

**Code de serveur qui définit l’objet complexe**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

**Code de serveur qui appelle la méthode du client à l’aide d’un objet complexe**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a>Comment appeler des méthodes de serveur à partir du client

Pour appeler une méthode de serveur à partir du client, utilisez le `server` propriété du proxy généré ou `invoke` méthode sur le concentrateur proxy si vous n’utilisez pas le proxy généré. La valeur de retour ou les paramètres peuvent être des objets complexes.

Passez dans une version de casse mixte du nom de la méthode du concentrateur. SignalR effectue automatiquement cette modification afin que le code JavaScript peut être conforme aux conventions de JavaScript.

Les exemples suivants montrent comment appeler une méthode de serveur qui n’a pas une valeur de retournée et appeler une méthode de serveur qui n’a pas une valeur de retour.

**Méthode de serveur sans attribut HubMethodName**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

**Code de serveur qui définit l’objet complexe passées dans un paramètre**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

**Code client qui appelle la méthode de serveur (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

**Code client qui appelle la méthode de serveur (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

Si vous décorée avec la méthode de concentrateur un `HubMethodName` d’attribut, utilisez ce nom sans changement de casse.

**Méthode serveur** avec un attribut HubMethodName

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

**Code client qui appelle la méthode de serveur (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

**Code client qui appelle la méthode de serveur (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

Les exemples suivants montrent comment appeler une méthode de serveur qui n’a aucune valeur de retour. Les exemples suivants montrent comment appeler une méthode de serveur qui a une valeur de retour.

**Code de serveur pour une méthode qui a une valeur de retour**

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

**La classe de Stock utilisée pour le** valeur de retour

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

**Code client qui appelle la méthode de serveur (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

**Code client qui appelle la méthode de serveur (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a>Comment gérer les événements de durée de vie de connexion

SignalR fournit des événements de durée de vie que vous pouvez gérer la connexion suivante :

- `starting`: Déclenché avant l’envoi des données via la connexion.
- `received`: Déclenché lorsque des données sont reçues sur la connexion. Fournit les données reçues.
- `connectionSlow`: Déclenché lorsque le client détecte une connexion lente ou suppression fréquemment.
- `reconnecting`: Déclenché lorsque le transport sous-jacent commence la reconnexion.
- `reconnected`: Déclenché lorsque le transport sous-jacent s’est reconnecté.
- `stateChanged`: Déclenché lorsque l’état de la connexion change. Fournit l’ancien état et le nouvel état (connexion, connecté, reconnexion ou Disconnected).
- `disconnected`: Déclenché lors de la connexion s’est déconnecté.

Par exemple, si vous souhaitez afficher les messages d’avertissement lorsqu’il existe des problèmes de connexion qui peuvent entraîner des retards notables, gérer les `connectionSlow` événement.

**Gérer l’événement connectionSlow (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

**Gérer l’événement connectionSlow (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

Pour plus d’informations, consultez [compréhension et gestion des événements de durée de vie de connexion dans SignalR](handling-connection-lifetime-events.md).

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a>Comment gérer les erreurs

Le client SignalR JavaScript fournit un `error` événement que vous pouvez ajouter un gestionnaire pour. Vous pouvez également utiliser la méthode fail pour ajouter un gestionnaire d’erreurs qui résultent d’un appel de méthode de serveur.

Si vous n’activez pas explicitement des messages d’erreur détaillés sur le serveur, l’objet exception qui SignalR renvoie une erreur et contient un minimum d’informations sur l’erreur. Par exemple, si un appel à `newContosoChatMessage` échoue, le message d’erreur dans l’objet d’erreur contient «`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`« envoi de messages d’erreur détaillés pour les clients en production n’est pas recommandé pour des raisons de sécurité, mais si vous souhaitez activer les messages d’erreur détaillés pour à des fins de résolution des problèmes, utilisez le code suivant sur le serveur.

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

L’exemple suivant montre comment ajouter un gestionnaire pour l’événement d’erreur.

**Ajouter un gestionnaire d’erreurs (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

**Ajouter un gestionnaire d’erreurs (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

L’exemple suivant montre comment gérer une erreur à partir d’un appel de méthode.

**Gérer une erreur à partir d’un appel de méthode (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

**Gérer une erreur à partir d’un appel de méthode (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

Si un appel de méthode échoue, le `error` événement est également déclenché, de sorte que votre code dans le `error` Gestionnaire de méthode et dans le `.fail` rappel de la méthode s’exécute.

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a>Comment activer la journalisation côté client

Pour activer la journalisation de côté client sur une connexion, définissez la `logging` propriété sur l’objet de connexion avant d’appeler le `start` méthode pour établir la connexion.

**Activer la journalisation (avec le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

**Activer la journalisation (sans le proxy généré)**

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

Pour afficher les journaux, ouvrez les outils de développement de votre navigateur et accédez à l’onglet de la Console. Pour obtenir un didacticiel qui montre des instructions détaillées et écran de captures qui montrent comment effectuer cette opération, consultez [avec ASP.NET Signalr - activer la journalisation de diffusion serveur](../getting-started/tutorial-server-broadcast-with-signalr.md#enablelogging).

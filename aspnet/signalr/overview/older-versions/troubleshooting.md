---
uid: signalr/overview/older-versions/troubleshooting
title: "Résolution des problèmes de SignalR (SignalR 1.x) | Documents Microsoft"
author: pfletcher
description: "Cet article décrit les problèmes courants avec le développement d’applications SignalR."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/05/2013
ms.topic: article
ms.assetid: 347210ba-c452-4feb-886f-b51d89f58971
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: eb739f806eb57d09008fa0bf04b5a40721dab73d
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="signalr-troubleshooting-signalr-1x"></a>Résolution des problèmes de SignalR (SignalR 1.x)
====================
par [Patrick Fletcher](https://github.com/pfletcher)

> Ce document décrit la résolution des problèmes courants avec SignalR.


Ce document contient les sections suivantes.

- [Appel de méthodes entre le client et le serveur échoue en mode silencieux](#connection)
- [Autres problèmes de connexion](#other)
- [Erreurs de compilation et côté serveur](#server)
- [Problèmes de Visual Studio](#vs)
- [Problèmes de Internet Information Services](#iis)
- [Problèmes de Azure](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a>Appel de méthodes entre le client et le serveur échoue en mode silencieux

Cette section décrit les causes possibles d’un appel de méthode entre client et serveur échoue sans message d’erreur explicite. Dans une application SignalR, le serveur n’a aucune information sur les méthodes que le client implémente ; Lorsque le serveur appelle une méthode du client, les données de paramètres et de nom de méthode sont envoyées au client, et la méthode est exécutée uniquement si elle existe dans le format que le serveur spécifié. Si aucune méthode correspondante n’est trouvée sur le client, rien ne se produit, et aucun message d’erreur n’est déclenchée sur le serveur.

Pour examiner les méthodes client ne pas appelés, vous pouvez activer la journalisation avant d’appeler la méthode start sur le concentrateur pour voir les appels sont provenant du serveur. Pour activer la journalisation dans une application JavaScript, consultez [comment activer la journalisation côté client (version du client JavaScript)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging). Pour activer la journalisation dans une application cliente .NET, consultez [comment activer la journalisation côté client (version Client .NET)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a>Méthode mal orthographié, signature de méthode incorrect ou nom de hub incorrect

Si le nom ou la signature d’une méthode appelée ne correspond pas exactement à une méthode appropriée sur le client, l’appel échoue. Vérifiez que le nom de la méthode appelé par le serveur correspond au nom de la méthode sur le client. SignalR crée également le concentrateur proxy à l’aide des méthodes de casse mixte, comme cela est approprié dans JavaScript, par conséquent, une méthode appelée `SendMessage` sur le serveur est appelé `sendMessage` dans le proxy client. Si vous utilisez la `HubName` d’attribut dans votre code côté serveur, vérifiez que le nom utilisé correspond au nom utilisé pour créer le concentrateur sur le client. Si vous n’utilisez pas le `HubName` d’attribut, vérifiez que le nom du concentrateur dans un client JavaScript est mixte, tels que chatHub au lieu de ChatHub.

### <a name="duplicate-method-name-on-client"></a>Nom de la méthode en double sur le client

Vérifiez que vous n’avez pas une méthode en double sur le client qui diffère uniquement par la casse. Si votre application cliente a une méthode appelée `sendMessage`, vérifiez qu’il n’est pas également une méthode appelée `SendMessage` également.

### <a name="missing-json-parser-on-the-client"></a>Analyseur JSON manquant sur le client

SignalR requiert un analyseur JSON doivent être présents pour sérialiser les appels entre le serveur et le client. Si votre client ne dispose d’un analyseur JSON intégré (par exemple, Internet Explorer 7), vous devez inclure dans votre application. Vous pouvez télécharger l’analyseur JSON [ici](http://nuget.org/packages/json2).

### <a name="mixing-hub-and-persistentconnection-syntax"></a>Le mélange des syntaxes Hub et persistentconnection n'

SignalR utilise deux modèles de communication : concentrateurs et PersistentConnections. La syntaxe d’appel de ces modèles de deux communication est différente dans le code client. Si vous avez ajouté un concentrateur dans le code de votre serveur, vérifiez que tout votre code client utilise la syntaxe correcte de concentrateur.

**Code JavaScript client qui crée un persistentconnection ne dans un client JavaScript**

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

**Code JavaScript client qui crée un Proxy de Hub dans un client Javascript**

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

**Code de serveur c# qui mappe un itinéraire vers un persistentconnection n'**

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

**Code de serveur c# qui mappe un itinéraire à un concentrateur ou à plusieurs concentrateurs si vous possédez plusieurs applications**

[!code-csharp[Main](troubleshooting/samples/sample4.cs)]

### <a name="connection-started-before-subscriptions-are-added"></a>Connexion démarrée avant que les abonnements sont ajoutés.

Si les connexions de concentrateur sont démarrée avant que les méthodes qui peuvent être appelées à partir du serveur sont ajoutées au proxy, les messages ne seront pas reçus. Le code JavaScript suivant ne démarre pas correctement le hub :

**Code client JavaScript incorrect qui n’autorise pas la réception de messages concentrateurs**

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

Au lieu de cela, ajoutez les abonnements de la méthode avant d’appeler Start :

**Code JavaScript client qui ajoute correctement les abonnements à un concentrateur**

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a>Nom de la méthode manquante sur le concentrateur proxy.

Vérifiez que la méthode définie sur le serveur est abonnée sur le client. Même si le serveur définit la méthode, il doit encore être ajouté pour le proxy client. Méthodes peuvent être ajoutés pour le proxy client comme suit (Notez que la méthode est ajoutée à la `client` membre du concentrateur, pas le concentrateur directement) :

**Code JavaScript client qui ajoute des méthodes à un concentrateur proxy.**

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a>Concentrateur ou des méthodes de concentrateur ne pas déclarés comme Public

Pour être visible sur le client, l’implémentation du concentrateur et les méthodes doivent être déclarées comme `public`.

### <a name="accessing-hub-from-a-different-application"></a>L’accès au hub à partir d’une autre application

Les concentrateurs SignalR est accessible uniquement via des applications qui implémentent les clients SignalR. SignalR ne peut pas interagir avec d’autres bibliothèques de communication (par exemple, SOAP ou WCF web services). Si aucun client SignalR n’est disponible pour votre plateforme cible, vous ne peut pas accéder directement au point de terminaison du serveur.

### <a name="manually-serializing-data"></a>Sérialisation des données manuellement

SignalR utilise automatiquement JSON pour sérialiser votre méthode sans devoir de paramètres-là le faire vous-même.

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a>Méthode de concentrateur distant ne pas exécutée sur le client dans OnDisconnected (fonction)

Ce comportement est inhérent à la conception. Lorsque `OnDisconnected` est appelée, le concentrateur a déjà entré le `Disconnected` état, ce qui n’autorise pas davantage à appeler des méthodes de concentrateur.

**Code de serveur c# qui exécute le code dans l’événement OnDisconnected correctement**

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="connection-limit-reached"></a>Limite de connexions atteint

Lorsque vous utilisez la version complète d’IIS sur un système d’exploitation de client telles que Windows 7, une limite de 10 connexions est imposée. Lorsque vous utilisez un système d’exploitation client, utilisez IIS Express au lieu de cela pour éviter cette limite.

### <a name="cross-domain-connection-not-set-up-properly"></a>Connexion entre domaines ne pas correctement configuré

Si une connexion entre domaines (une connexion pour laquelle l’URL SignalR n’est pas dans le même domaine que la page d’hébergement) n’est pas configurée correctement, la connexion échoue sans message d’erreur. Pour plus d’informations sur la façon d’activer la communication entre domaines, consultez [comment établir une connexion entre domaines](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a>Connexion à l’aide de NTLM (Active Directory) ne fonctionne ne pas dans un client .NET

Une connexion dans une application cliente .NET qui utilise la sécurité de domaine peut échouer si la connexion n’est pas configurée correctement. Pour utiliser SignalR dans un environnement de domaine, définissez la propriété de connexion requises comme suit :

**Code de client c# qui implémente les informations d’identification de connexion**

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="other"></a>

## <a name="other-connection-issues"></a>Autres problèmes de connexion

Cette section décrit les causes et solutions pour les problèmes spécifiques ou des messages d’erreur qui se produisent lors d’une connexion.

### <a name="start-must-be-called-before-data-can-be-sent-error"></a>Erreur de « Début doit être appelé avant l’envoi de données »

Cette erreur survient généralement si le code fait référence à des objets de SignalR avant le démarrage de la connexion. L’automatiques pour les gestionnaires et similaires que seront appellent des méthodes définies sur le serveur doit être ajoutés une fois la connexion terminée. Notez que l’appel à `Start` est asynchrone, afin de code après l’appel peut être exécuté avant qu’elle se termine. La meilleure façon d’ajouter des gestionnaires d’après le début d’une connexion complètement est de les placer dans une fonction de rappel qui est passée en tant que paramètre à la méthode de démarrage :

**Code JavaScript client qui ajoute correctement les gestionnaires d’événements qui font référence à des objets de SignalR**

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

Cette erreur apparaîtra également si une connexion s’arrête alors que les objets SignalR sont toujours référencés.

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a>« 301 déplacé définitivement » ou « 302 Déplacé temporairement » une erreur

Cette erreur peut se produire si le projet contient un dossier appelé SignalR, ce qui pourrait affecter le proxy créé automatiquement. Pour éviter cette erreur, n’utilisez pas un dossier appelé `SignalR` dans votre application, ou désactiver la génération automatique du proxy désactivé. Consultez [le Proxy généré et ce qu’il fait pour vous](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) pour plus d’informations.

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a>Erreur « 403 Interdit » dans le client .NET ou Silverlight

Cette erreur peut se produire dans des environnements de domaines où la communication entre domaines n’est pas activée correctement. Pour plus d’informations sur la façon d’activer la communication entre domaines, consultez [comment établir une connexion entre domaines](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain). Pour établir une connexion entre domaines dans un client Silverlight, consultez [connexions inter-domaines à partir de clients Silverlight](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).

### <a name="404-not-found-error"></a>Erreur « 404 introuvable »

Il existe plusieurs causes possibles pour résoudre ce problème. Vérifiez que tous les éléments suivants :

- **Référence d’adresse proxy Hub ne pas correctement mis en forme :** cette erreur se produit couramment si la référence à l’adresse du proxy généré hub n’est pas formatée correctement. Vérifiez que la référence à l’adresse du concentrateur est correctement effectuée. Consultez [comment référencer le proxy généré dynamiquement](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) pour plus d’informations.
- **Ajout d’itinéraires à l’application avant d’ajouter l’itinéraire du concentrateur :** si votre application utilise d’autres itinéraires, assurez-vous que le premier itinéraire ajouté l’appel à `MapHubs`.

### <a name="500-internal-server-error"></a>« Erreur de serveur interne 500 »

Il s’agit d’une erreur très générique qui peut avoir un large éventail de causes. Les détails de l’erreur doivent apparaître dans le journal des événements du serveur, ou sont accessibles via le débogage du serveur. Plus d’informations sur l’erreur peuvent être obtenues en activant les erreurs détaillées sur le serveur. Pour plus d’informations, consultez [comment gérer les erreurs dans la classe de concentrateur](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).

### <a name="typeerror-lthubtypegt-is-undefined-error"></a>« TypeError : &lt;hubType&gt; n’est pas défini » erreur

Cette erreur se produit si l’appel à `MapHubs` n’est pas effectuée correctement. Consultez [comment enregistrer l’itinéraire SignalR et de configurer les options de SignalR](../guide-to-the-api/hubs-api-guide-server.md#route) pour plus d’informations.

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a>JsonSerializationException a été gérée par le code utilisateur

Vérifiez que les paramètres que vous envoyez à vos méthodes n’incluent pas les types non sérialisable (telles que les handles de fichiers ou des connexions de base de données). Si vous avez besoin d’utiliser des membres sur un objet côté serveur que vous ne souhaitez pas être envoyé au client (soit pour la sécurité ou pour des raisons de sérialisation), utilisez la `JSONIgnore` attribut.

### <a name="protocol-error-unknown-transport-error"></a>« Erreur de protocole : transport inconnu « erreur

Cette erreur peut se produire si le client ne prend pas en charge les transports SignalR utilise. Consultez [du transport et secours](../getting-started/introduction-to-signalr.md#transports) pour plus d’informations sur lequel les navigateurs peuvent être utilisés avec SignalR.

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a>« Génération de proxy de JavaScript Hub a été désactivée. »

Cette erreur se produit si `DisableJavaScriptProxies` est définie durant également y compris une référence au proxy générée dynamiquement à `signalr/hubs`. Pour plus d’informations sur la création du proxy manuellement, consultez [du proxy généré et ce qu’il fait pour vous](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a>« L’ID de connexion est dans un format incorrect » ou « l’identité de l’utilisateur ne peut pas modifier durant une connexion SignalR active » erreur

Cette erreur peut se produire si l’authentification est utilisée et que le client est déconnecté avant l’arrêt de la connexion. La solution consiste à arrêter la connexion SignalR avant la déconnexion du client.

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a>« Non interceptée erreur : SignalR : jQuery introuvable. Vérifiez que jQuery est référencé avant le fichier SignalR.js » une erreur

Le client SignalR JavaScript nécessite jQuery à exécuter. Vérifiez que votre référence à jQuery est correct, que le chemin d’accès est valide et que la référence à jQuery est la référence à SignalR.

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a>« Non interceptée TypeError : Impossible de lire propriété '&lt;propriété&gt;' undefined « erreur

Cette erreur se produit à partir de n’ayant ne pas jQuery ou le proxy de concentrateurs correctement référencés. Vérifiez que votre référence de jQuery et le proxy de concentrateurs est correct, que le chemin d’accès est valide et que la référence à jQuery est la référence au proxy de concentrateurs. La référence par défaut pour le proxy de concentrateurs doit se présenter comme suit :

**Code HTML côté client qui référence correctement le proxy de concentrateurs**

[!code-html[Main](troubleshooting/samples/sample11.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a>Erreur de « RuntimeBinderException a été gérée par le code utilisateur »

Cette erreur peut se produire lorsque la surcharge incorrecte de `Hub.On` est utilisé. Si la méthode a une valeur de retour, le type de retour doit être spécifié en tant que paramètre de type générique :

**Méthode définie sur le client (sans proxy généré)**

[!code-html[Main](troubleshooting/samples/sample12.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a>ID de connexion est incohérent ou connexion sauts entre les chargements de page

Ce comportement est inhérent à la conception. Étant donné que le concentrateur est hébergé dans l’objet de la page, le concentrateur est détruit lorsque la page est actualisée. A besoin d’une application comportant plusieurs page maintenir l’association entre les utilisateurs et les ID de connexion afin qu’ils seront cohérentes d’un chargement de page. L’ID de connexion peuvent être stockée sur le serveur, que ce soit un `ConcurrentDictionary` objet ou une base de données.

### <a name="value-cannot-be-null-error"></a>« La valeur ne peut pas être null » une erreur

Méthodes côté serveur avec des paramètres optionnels ne sont pas actuellement pris en charge ; Si le paramètre facultatif est omis, la méthode échoue. Pour plus d’informations, consultez [paramètres facultatifs](https://github.com/SignalR/SignalR/issues/324).

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a>« Firefox ne peut pas établir une connexion au serveur &lt;adresse&gt;« erreur dans Firebug

Ce message d’erreur sont visibles dans Firebug si la négociation du transport WebSocket échoue et un autre transport est utilisé à la place. Ce comportement est inhérent à la conception.

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a>« Le certificat distant n’est pas valide selon la procédure de validation » une erreur dans l’application cliente .NET

Si votre serveur nécessite des certificats de client personnalisé, vous pouvez ajouter un x509certificate à la connexion avant que la demande est effectuée. Ajouter le certificat à la connexion à l’aide de `Connection.AddClientCertificate`.

### <a name="connection-drops-after-authentication-times-out"></a>Connexion supprime après l’expiration de l’authentification

Ce comportement est inhérent à la conception. Informations d’identification d’authentification ne peut pas être modifiées lorsqu’une connexion est active ; Pour actualiser les informations d’identification, la connexion doit être arrêtée et redémarrée.

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a>OnConnected est appelée deux fois à l’aide de jQuery Mobile

jQuery Mobile `initializePage` fonction force les scripts dans chaque page d’être exécutée à nouveau, créant ainsi une deuxième connexion. Les solutions à ce problème sont les suivantes :

- Inclure la référence à jQuery Mobile avant votre fichier JavaScript.
- Désactiver la `initializePage` fonction en définissant `$.mobile.autoInitializePage = false`.
- Attendez que la page pour terminer l’initialisation avant de démarrer la connexion.

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a>Les messages sont en retard dans les applications Silverlight à l’aide d’événements envoyés du serveur

Les messages sont différés à l’aide de serveur d’envoyées des événements sur Silverlight. Pour forcer le long de l’interrogation pour être utilisé à la place, utilisez les éléments suivants lors du démarrage de la connexion :

[!code-css[Main](troubleshooting/samples/sample13.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a>À l’aide de « Autorisation refusée » Frame indéfiniment de protocole

Il s’agit d’un problème connu, décrit [ici](https://github.com/SignalR/SignalR/issues/1963). Ce problème peut se produire à l’aide de la bibliothèque JQuery dernière ; la solution consiste à mettre à niveau votre application pour JQuery 1.8.2.

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a>Erreurs de compilation et côté serveur

 La section suivante contient des solutions possibles au compilateur et les erreurs d’exécution du côté serveur. 

### <a name="reference-to-hub-instance-is-null"></a>Référence à l’instance de concentrateur a la valeur null

Dans la mesure où une instance de concentrateur est créée pour chaque connexion, Impossible de créer une instance d’un concentrateur dans votre code vous-même. Pour appeler des méthodes sur un concentrateur à l’extérieur du hub lui-même, consultez [comment appeler des méthodes de client et de gérer des groupes à l’extérieur de la classe de concentrateur](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) pour savoir comment obtenir une référence pour le contexte du concentrateur.

### <a name="httpcontextcurrentsession-is-null"></a>HTTPContext.Current.Session a la valeur null

Ce comportement est inhérent à la conception. SignalR ne prend pas en charge l’état de session ASP.NET, étant donné que l’activation de l’état de session compromettrait de messagerie duplex.

### <a name="no-suitable-method-to-override"></a>Aucune méthode appropriée pour remplacer

Vous pouvez voir cette erreur si vous utilisez le code à partir de la documentation plus ancienne ou des blogs. Vérifiez que vous référencez pas les noms des méthodes qui ont été modifiés ou déconseillés (comme `OnConnectedAsync`).

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a>HostContextExtensions.WebSocketServerUrl a la valeur null

Ce comportement est inhérent à la conception. Ce membre est déconseillé et ne doit pas être utilisé.

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a>Erreur « un itinéraire nommé 'signalr.hubs' est déjà dans la collection d’itinéraires »

Cette erreur se produira si `MapHubs` est appelée deux fois par votre application. Certaines applications exemple d’appel `MapHubs` directement dans le fichier d’application globale ; d’autres effectuer l’appel dans une classe wrapper. Assurez-vous que votre application n’effectue pas les deux.

<a id="vs"></a>

## <a name="visual-studio-issues"></a>Problèmes de Visual Studio

Cette section décrit les problèmes rencontrés dans Visual Studio.

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a>Nœud de Documents de script n’apparaît pas dans l’Explorateur de solutions

Certaines de nos didacticiels vous dirigent vers le nœud « Documents de Script » dans l’Explorateur de solutions pendant le débogage. Ce nœud est généré par le débogueur JavaScript et s’affiche uniquement lors du débogage des clients de navigateur dans Internet Explorer ; le nœud n’apparaît pas si Chrome ou Firefox est utilisé. Le débogueur JavaScript également exécutera pas si un autre débogueur client est en cours d’exécution, telles que le débogueur Silverlight.

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a>SignalR ne fonctionne pas sur Visual Studio 2008 ou version antérieure

Ce comportement est inhérent à la conception. SignalR nécessite .NET Framework 4 ou version ultérieure ; Cela nécessite que les applications SignalR être développées dans Visual Studio 2010 ou version ultérieure.

<a id="iis"></a>

## <a name="iis-issues"></a>Problèmes d’IIS

Cette section contient des problèmes avec Internet Information Services.

### <a name="web-site-crashes-after-maphubs-call"></a>Site Web se bloque après l’appel de MapHubs

Ce problème a été résolu dans la dernière version de SignalR. Vérifiez que vous utilisez la dernière version publiée de SignalR en mettant à jour votre installation à l’aide de NuGet.

<a id="azure"></a>

## <a name="azure-issues"></a>Problèmes de Azure

Cette section contient des problèmes avec Microsoft Azure.

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a>Les messages ne sont pas reçues via l’infrastructure d’intégration Azure après la modification des noms de rubrique

Les rubriques utilisées par l’infrastructure d’intégration Azure ne sont pas destinées à être configurables par l’utilisateur.

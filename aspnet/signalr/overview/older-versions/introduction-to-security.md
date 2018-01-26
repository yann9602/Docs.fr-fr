---
uid: signalr/overview/older-versions/introduction-to-security
title: "Introduction à la sécurité de SignalR (SignalR 1.x) | Documents Microsoft"
author: pfletcher
description: "Décrit les problèmes de sécurité que vous devez prendre en compte lorsque vous développez une application SignalR."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/17/2013
ms.topic: article
ms.assetid: 715a4059-d307-4631-abbb-c789c95d6eb4
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/introduction-to-security
msc.type: authoredcontent
ms.openlocfilehash: ebc83098b73902fa3f7a90a38dafc43b413e75fe
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="introduction-to-signalr-security-signalr-1x"></a>Introduction à la sécurité de SignalR (SignalR 1.x)
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit les problèmes de sécurité que vous devez prendre en compte lorsque vous développez une application SignalR.


## <a name="overview"></a>Vue d'ensemble

Ce document contient les sections suivantes :

- [Concepts de sécurité SignalR](#concepts)

    - [Authentification et autorisation](#authentication)
    - [Jeton de connexion](#connectiontoken)
    - [Lors de la reconnexion réintégration des groupes](#rejoingroup)
- [Comment SignalR empêche Cross-Site Request Forgery](#csrf)
- [Recommandations de sécurité pour SignalR](#recommendations)

    - [Protocole SSL (Socket Layers) sécurisé](#ssl)
    - [N’utilisez pas de groupes en tant que mécanisme de sécurité](#groupsecurity)
    - [Gestion en toute sécurité de l’entrée à partir de clients](#input)
    - [Rapprochement un changement d’état d’utilisateur avec une connexion active](#reconcile)
    - [Fichiers de proxy JavaScript générés automatiquement](#autogen)
    - [Exceptions](#exceptions)

<a id="concepts"></a>

## <a name="signalr-security-concepts"></a>Concepts de sécurité SignalR

<a id="authentication"></a>

### <a name="authentication-and-authorization"></a>Authentification et autorisation

SignalR est conçu pour être intégré dans la structure existante de l’authentification pour une application. Il ne fournit pas toutes les fonctionnalités d’authentification des utilisateurs. Au lieu de cela, vous authentifier les utilisateurs que vous le feriez normalement dans votre application, puis utiliser les résultats de l’authentification dans votre code SignalR. Par exemple, vous pouvez authentifier les utilisateurs avec l’authentification par formulaire ASP.NET et ensuite appliquer les utilisateurs dans votre concentrateur, ou des rôles sont autorisés à appeler une méthode. Dans votre concentrateur, vous pouvez également passer des informations d’authentification, telles que le nom d’utilisateur ou si un utilisateur appartient à un rôle, et le client.

SignalR fournit le [Authorize](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribut pour spécifier les utilisateurs qui ont accès à une méthode ou un concentrateur. Vous appliquez l’attribut Authorize à un concentrateur ou des méthodes dans un concentrateur. Sans l’attribut Authorize, toutes les méthodes publiques sur le concentrateur sont disponibles pour un client qui est connecté au concentrateur. Pour plus d’informations sur les concentrateurs, consultez [authentification et autorisation pour les concentrateurs SignalR](../security/hub-authorization.md).

Le `Authorize` attribut est utilisé uniquement avec des concentrateurs. Pour appliquer des règles d’autorisation lorsque vous utilisez un `PersistentConnection` vous devez substituer la `AuthorizeRequest` (méthode). Pour plus d’informations sur les connexions persistantes, consultez [authentification et autorisation pour les connexions persistantes SignalR](../security/persistent-connection-authorization.md).

<a id="connectiontoken"></a>

### <a name="connection-token"></a>Jeton de connexion

SignalR atténue le risque de l’exécution de commandes malveillantes par la validation de l’identité de l’expéditeur. Un jeton de connexion, qui contient l’id de connexion et le nom d’utilisateur pour les utilisateurs authentifiés, est transmis entre le client et le serveur pour chaque demande. L’id de connexion est un identificateur unique qui est généré de façon aléatoire par le serveur lorsqu’une nouvelle connexion est créée et est rendue persistante pour la durée de la connexion. Le nom d’utilisateur est fournie par le mécanisme d’authentification pour l’application web. Le jeton de connexion est protégé par chiffrement et une signature numérique.

![](introduction-to-security/_static/image2.png)

Pour chaque demande, le serveur valide le contenu du jeton pour vous assurer que la demande provient de l’utilisateur spécifié. Le nom d’utilisateur doit correspondre à l’id de connexion. En validant l’id de connexion et le nom d’utilisateur, un utilisateur malveillant empêche SignalR de facilement empruntant l’identité d’un autre utilisateur. Si le serveur ne peut pas valider le jeton de connexion, la demande échoue.

![](introduction-to-security/_static/image4.png)

Comme l’id de connexion fait partie du processus de vérification, vous ne devez pas révéler des id de connexion d’un utilisateur à d’autres utilisateurs ou stocker la valeur sur le client, comme dans un cookie.

<a id="rejoingroup"></a>

### <a name="rejoining-groups-when-reconnecting"></a>Lors de la reconnexion réintégration des groupes

Par défaut, l’application SignalR nouveau attribue automatiquement un utilisateur aux groupes appropriés lors de la reconnexion à partir d’une interruption temporaire, tels que lorsqu’une connexion est supprimée ou rétablie avant l’expiration de la connexion. Lors de la reconnexion, le client passe un jeton de groupe qui inclut l’id de connexion et les groupes assignés. Le jeton de groupe est numériquement signé et chiffré. Le client conserve le même id de connexion après une reconnexion ; Par conséquent, l’id de connexion transmis par le client reconnecté doit correspondre à l’id de connexion précédente utilisée par le client. Cette vérification empêche la transmission des demandes d’adhésion des groupes non autorisés lors de la reconnexion à un utilisateur malveillant.

Toutefois, il est important de noter que le jeton de groupe n’expire pas. Si un utilisateur appartenance à un groupe dans le passé, mais a été exclu (e) à partir de ce groupe, que l’utilisateur est peut-être en mesure de reproduire un jeton de groupe qui inclut le groupe interdit. Si vous avez besoin gérer les utilisateurs qui appartiennent à quels groupes, vous devez stocker ces données sur le serveur, comme dans une base de données. Ensuite, ajouter une logique à votre application qui vérifie sur le serveur si un utilisateur appartenance à un groupe. Pour obtenir un exemple de vérification de l’appartenance au groupe, consultez [utilisation de groupes de](../guide-to-the-api/working-with-groups.md).

Réintégration des groupes s’applique uniquement lorsqu’une connexion est reconnectée après une interruption temporaire. Si un utilisateur se déconnecte par vous obliger à quitter l’application ou le redémarrage de l’application, votre application doit gérer la procédure ajouter cet utilisateur aux groupes appropriés. Pour plus d’informations, consultez [utilisation de groupes de](../guide-to-the-api/working-with-groups.md).

<a id="csrf"></a>

## <a name="how-signalr-prevents-cross-site-request-forgery"></a>Comment SignalR empêche Cross-Site Request Forgery

Falsification de requête intersites (CSRF) est une attaque où un site malveillant envoie une demande à un site vulnérable où l’utilisateur est actuellement connecté. SignalR empêche CSRF en le rendant très peu de chances d’un site malveillant pour créer une requête valide pour votre application SignalR.

### <a name="description-of-csrf-attack"></a>Description de l’attaque CSRF

Voici un exemple d’une attaque CSRF :

1. Un utilisateur se connecte en www.example.com, à l’aide de l’authentification par formulaire.
2. Le serveur authentifie l’utilisateur. La réponse du serveur inclut un cookie d’authentification.
3. Sans la déconnexion, l’utilisateur visite un site web malveillant. Ce site malveillant contient le formulaire HTML suivant : 

    [!code-html[Main](introduction-to-security/samples/sample1.html)]

 Notez que l’action de formulaire valide sur le site vulnérable, pas pour le site malveillant. Il s’agit de la partie « cross-site » de CSRF.
4. L’utilisateur clique sur le bouton Envoyer. Le navigateur inclut le cookie d’authentification avec la demande.
5. La requête s’exécute sur le serveur example.com avec le contexte de l’authentification de l’utilisateur et peut faire tout ce qu’un utilisateur authentifié est autorisé à faire.

Bien que cet exemple requiert que l’utilisateur de cliquer sur le bouton du formulaire, la page malveillante pourrait aussi facilement exécuter un script qui envoie une requête AJAX à votre application SignalR. En outre, l’utilisation de SSL n’empêche pas une attaque CSRF, car le site malveillant peut envoyer une demande de « https:// ».

En règle générale, les attaques CSRF sont possibles contre les sites web qui utilisent des cookies pour l’authentification, étant donné que les navigateurs envoient tous les cookies pertinentes pour le site de destination. Toutefois, les attaques CSRF n’êtes pas limités pour exploiter les cookies. Par exemple, l’authentification de base et Digest sont également vulnérables. Une fois un utilisateur se connecte avec l’authentification de base ou Digest, le navigateur envoie automatiquement les informations d’identification jusqu'à ce que la session se termine.

### <a name="csrf-mitigations-taken-by-signalr"></a>Solutions d’atténuation CSRF prises par SignalR

SignalR effectue les étapes suivantes pour empêcher un site malveillant à partir de la création de demandes valides pour votre application SignalR. Ces étapes sont effectuées par défaut et ne nécessitent pas d’action dans votre code.

- **Désactiver des requêtes entre domaines**  
 Par défaut, plusieurs domaines, les demandes sont désactivées dans une application SignalR pour empêcher les utilisateurs de l’appel d’un point de terminaison SignalR à partir d’un domaine externe. Toute demande qui provient d’un domaine externe est automatiquement considéré comme non valide et est bloqué. Il est recommandé de conserver ce comportement par défaut ; Sinon, un site malveillant peut amener les utilisateurs d’envoyer des commandes à votre site. Si vous devez utiliser des requêtes entre domaines, consultez [comment établir une connexion entre domaines](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain) .
- **Passez le jeton de connexion dans la chaîne de requête, pas de cookie**  
 SignalR transmet le jeton de connexion sous la forme d’une valeur de chaîne de requête, au lieu d’en tant que cookie. Stocke le jeton de connexion en tant que cookie, le jeton de connexion n’est pas par inadvertance transmis par le navigateur lorsqu’un code malveillant est détecté. En outre, le jeton de connexion n’est pas conservé au-delà de la connexion actuelle. Par conséquent, un utilisateur malveillant ne peut pas faire la demande sous les informations d’identification d’un autre utilisateur.
- **Vérifier le jeton de connexion**  
 Comme décrit dans la [jeton de connexion](#connectiontoken) section, le serveur sait que l’id de connexion est associé à chaque utilisateur authentifié. Le serveur ne traite pas les demandes d’un id de connexion qui ne correspond pas le nom d’utilisateur. Il est peu probable qu'un utilisateur malveillant pourrait deviner une demande valide, car l’utilisateur malveillant devrait connaître le nom d’utilisateur et l’id généré de façon aléatoire la connexion actuelle. Cet id de connexion n’est plus valide dès que la connexion est terminée. Les utilisateurs anonymes n’a pas doivent avoir accès à toutes les informations sensibles.

<a id="recommendations"></a>

## <a name="signalr-security-recommendations"></a>Recommandations de sécurité pour SignalR

<a id="ssl"></a>

### <a name="secure-socket-layers-ssl-protocol"></a>Protocole SSL (Socket Layers) sécurisé

Le protocole SSL utilise le chiffrement pour sécuriser le transport des données entre un client et le serveur. Si votre application SignalR transmet des informations sensibles entre le client et le serveur, utilisez SSL pour le transport. Pour plus d’informations sur la configuration de SSL, consultez [comment configurer SSL sur IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

<a id="groupsecurity"></a>

### <a name="do-not-use-groups-as-a-security-mechanism"></a>N’utilisez pas de groupes en tant que mécanisme de sécurité

Les groupes sont un moyen pratique de collecte des utilisateurs liés, mais ils ne sont pas un mécanisme sécurisé pour limiter l’accès aux informations sensibles. Cela est particulièrement vrai lorsque les utilisateurs peuvent rejoindre automatiquement les groupes pendant une reconnexion. Au lieu de cela, envisagez d’ajouter des utilisateurs disposant de privilèges à un rôle et la limitation de l’accès à une méthode de concentrateur pour seulement les membres de ce rôle. Pour obtenir un exemple de limiter l’accès basé sur un rôle, consultez [authentification et autorisation pour les concentrateurs SignalR](../security/hub-authorization.md). Pour obtenir un exemple de vérification des accès utilisateur aux groupes lors de la reconnexion, consultez [utilisation de groupes de](../guide-to-the-api/working-with-groups.md).

<a id="input"></a>

### <a name="safely-handling-input-from-clients"></a>Gestion en toute sécurité de l’entrée à partir de clients

Toutes les entrées à partir de clients qui sont destinée à la diffusion à d’autres clients doivent être codées pour vous assurer qu’un utilisateur malveillant n’envoie pas de script à d’autres utilisateurs. Il est préférable encoder des messages sur les clients de réception plutôt que sur le serveur, car votre application SignalR peut-être avoir différents types de clients. Par conséquent, l’encodage HTML fonctionne pour un client web, mais pas pour d’autres types de clients. Par exemple, une méthode de client web pour afficher un message en toute sécurité traitera le nom d’utilisateur et le message en appelant le `html()` (fonction).

[!code-html[Main](introduction-to-security/samples/sample2.html?highlight=3-4)]

<a id="reconcile"></a>

### <a name="reconciling-a-change-in-user-status-with-an-active-connection"></a>Rapprochement un changement d’état d’utilisateur avec une connexion active

Si l’état d’authentification d’un utilisateur change alors qu’une connexion active existe, l’utilisateur recevra une erreur indiquant que, « l’identité de l’utilisateur ne peut pas modifier durant une connexion SignalR active ». Dans ce cas, votre application doit reconnecter au serveur pour vous assurer que l’id de connexion et le nom d’utilisateur sont coordonnés. Par exemple, si votre application permet à l’utilisateur déconnecter une connexion active existe, le nom d’utilisateur pour la connexion ne correspondront plus le nom est passé pour la demande suivante. Vous souhaitez arrêter la connexion avant que l’utilisateur se déconnecte et puis redémarrez-le.

Toutefois, il est important de noter que la plupart des applications ne devrez pas arrêter et démarrer la connexion manuellement. Si votre application redirige les utilisateurs vers une page distincte, une fois connecté, telles que le comportement par défaut dans une application Web Forms ou une application MVC ou actualise la page active après la déconnexion, la connexion active est automatiquement déconnectée et qu’il n’est pas nécessitent aucune action supplémentaire.

L’exemple suivant montre comment arrêter et démarrer une connexion lorsque l’état de l’utilisateur a été modifiée.

[!code-html[Main](introduction-to-security/samples/sample3.html)]

Ou bien, l’état d’authentification de l’utilisateur peut changer si votre site utilise expiration décalée avec l’authentification par formulaire, et il n’existe aucune activité pour conserver le cookie d’authentification valide. Dans ce cas, l’utilisateur sera déconnecté et le nom d’utilisateur ne correspondront plus le nom d’utilisateur dans le jeton de connexion. Vous pouvez résoudre ce problème en ajoutant un script qui demande périodiquement une ressource sur le serveur web pour conserver le cookie d’authentification valide. L’exemple suivant montre comment demander une ressource toutes les 30 minutes.

[!code-javascript[Main](introduction-to-security/samples/sample4.js)]

<a id="autogen"></a>

### <a name="automatically-generated-javascript-proxy-files"></a>Fichiers de proxy JavaScript générés automatiquement

Si vous ne souhaitez pas inclure tous les concentrateurs et méthodes dans le fichier proxy JavaScript pour chaque utilisateur, vous pouvez désactiver la génération automatique du fichier. Vous pouvez choisir cette option si vous avez plusieurs concentrateurs et méthodes, mais que vous ne souhaitez pas que tous les utilisateurs de connaître toutes les méthodes. Vous désactivez la génération automatique en définissant **EnableJavaScriptProxies** à **false**.

[!code-csharp[Main](introduction-to-security/samples/sample5.cs)]

Pour plus d’informations sur les fichiers de proxy JavaScript, consultez [du proxy généré et ce qu’il fait pour vous](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy). <a id="exceptions"></a>

### <a name="exceptions"></a>Exceptions

Vous devez éviter de passer des objets d’exception aux clients, car les objets peuvent exposer des informations sensibles sur les clients. Au lieu de cela, appelez une méthode sur le client qui affiche le message d’erreur pertinents.

[!code-csharp[Main](introduction-to-security/samples/sample6.cs)]

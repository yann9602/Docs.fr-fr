---
uid: signalr/overview/security/hub-authorization
title: Authentification et autorisation pour les concentrateurs SignalR | Documents Microsoft
author: pfletcher
description: "Cette rubrique décrit comment faire pour restreindre les utilisateurs ou les rôles peuvent accéder aux méthodes de concentrateur. Versions du logiciel utilisé dans cette rubrique Visual Studio 2013 .NET 4.5 SignalR ve..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/05/2015
ms.topic: article
ms.assetid: a610c796-c131-473c-baef-2e6c568cb2a2
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/security/hub-authorization
msc.type: authoredcontent
ms.openlocfilehash: f1538c933ff9e8e680d70ce1e63d24b189be47e5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="authentication-and-authorization-for-signalr-hubs"></a>Authentification et autorisation pour les concentrateurs SignalR
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

> Cette rubrique décrit comment faire pour restreindre les utilisateurs ou les rôles peuvent accéder aux méthodes de concentrateur. 
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

Cette rubrique contient les sections suivantes :

- [Autoriser l’attribut](#authorizeattribute)
- [Exiger l’authentification pour tous les concentrateurs](#requireauth)
- [Autorisation personnalisée](#custom)
- [Passer des informations d’authentification pour les clients](#passauth)
- [Options d’authentification pour les clients .NET](#authoptions)

    - [Cookie d’authentification par formulaires](#cookie)
    - [Authentification Windows](#windows)
    - [En-tête de connexion](#header)
    - [Certificat](#certificate)

<a id="authorizeattribute"></a>

## <a name="authorize-attribute"></a>Autoriser l’attribut

SignalR fournit le [Authorize](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.authorizeattribute(v=vs.111).aspx) attribut pour spécifier les utilisateurs ou les rôles ont accès à une méthode ou un concentrateur. Cet attribut se trouve dans le `Microsoft.AspNet.SignalR` espace de noms. Vous appliquez le `Authorize` d’attribut à un concentrateur ou des méthodes dans un concentrateur. Lorsque vous appliquez le `Authorize` attribut à une classe de concentrateur, l’exigence d’autorisation spécifié est appliqué à toutes les méthodes dans le concentrateur. Cette rubrique fournit des exemples des différents types de spécifications d’autorisation que vous pouvez appliquer. Sans le `Authorize` attribut connecté client peut accéder à n’importe quelle méthode publique sur le concentrateur.

Si vous avez défini un rôle nommé « Admin » dans votre application web, vous pouvez spécifier que seuls les utilisateurs de ce rôle peuvent accéder à un concentrateur avec le code suivant.

[!code-csharp[Main](hub-authorization/samples/sample1.cs)]

Ou bien, vous pouvez spécifier qu’un concentrateur contient une méthode qui est disponible pour tous les utilisateurs et une deuxième méthode est uniquement disponible pour les utilisateurs authentifiés, comme indiqué ci-dessous.

[!code-csharp[Main](hub-authorization/samples/sample2.cs)]

Les exemples suivants d’autorisation différents scénarios :

- `[Authorize]`: seuls les utilisateurs authentifiés
- `[Authorize(Roles = "Admin,Manager")]`: seuls les utilisateurs spécifiés aux rôles authentifiés
- `[Authorize(Users = "user1,user2")]`: seuls les utilisateurs avec des noms d’utilisateurs spécifiés authentifiés
- `[Authorize(RequireOutgoing=false)]`: seuls les utilisateurs authentifiés peuvent appeler le concentrateur, mais les appels à partir du serveur aux clients ne sont pas limités par l’autorisation, tels que, lorsque seuls certains utilisateurs peuvent envoyer un message, mais tous les autres utilisateurs peuvent recevoir le message. La propriété RequireOutgoing applicable uniquement pour le hub entier, pas sur les méthodes de personnes dans le concentrateur. RequireOutgoing n’est pas définie sur false, seuls les utilisateurs qui répond aux exigences d’autorisation sont appelés à partir du serveur.

<a id="requireauth"></a>

## <a name="require-authentication-for-all-hubs"></a>Exiger l’authentification pour tous les concentrateurs

Vous pouvez exiger l’authentification pour tous les concentrateurs et méthodes de concentrateur dans votre application en appelant le [RequireAuthentication](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.hubpipelineextensions.requireauthentication(v=vs.111).aspx) méthode lorsque l’application démarre. Vous pouvez utiliser cette méthode lorsque vous avez plusieurs concentrateurs et que vous souhaitez appliquer une exigence d’authentification pour l’ensemble des. Avec cette méthode, vous ne pouvez pas spécifier de configuration requise pour le rôle, un utilisateur ou d’autorisation sortante. Vous ne pouvez spécifier qu’accès aux méthodes de concentrateur l'est limité aux utilisateurs authentifiés. Toutefois, vous pouvez toujours appliquer l’attribut Authorize aux concentrateurs ou des méthodes pour spécifier des exigences supplémentaires. Aucune exigence que vous spécifiez dans un attribut est ajouté à la condition d’authentification de base.

L’exemple suivant montre un fichier de démarrage qui limite toutes les méthodes de concentrateur aux utilisateurs authentifiés.

[!code-csharp[Main](hub-authorization/samples/sample3.cs)]

Si vous appelez le `RequireAuthentication()` méthode après le traitement d’une requête SignalR, SignalR lèvera une `InvalidOperationException` exception. SignalR lève cette exception, car il est impossible d’ajouter un module à la HubPipeline une fois que le pipeline a été appelé. L’exemple précédent montre l’appel du `RequireAuthentication` méthode dans le `Configuration` méthode qui est exécutée une fois avant la première requête.

<a id="custom"></a>

## <a name="customized-authorization"></a>Autorisation personnalisée

Si vous avez besoin personnaliser la façon dont l’autorisation est définie, vous pouvez créer une classe qui dérive de `AuthorizeAttribute` et remplacez le [UserAuthorized](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.authorizeattribute.userauthorized(v=vs.111).aspx) (méthode). Pour chaque demande, SignalR appelle cette méthode pour déterminer si l’utilisateur est autorisé à traiter la demande. Dans la méthode substituée, vous fournir la logique nécessaire pour votre scénario d’autorisation. L’exemple suivant montre comment appliquer l’autorisation via une identité basée sur les revendications.

[!code-csharp[Main](hub-authorization/samples/sample4.cs)]

<a id="passauth"></a>

## <a name="pass-authentication-information-to-clients"></a>Passer des informations d’authentification pour les clients

Vous devrez peut-être utiliser les informations d’authentification dans le code qui s’exécute sur le client. Vous transmettez les informations requises lors de l’appel de méthodes sur le client. Par exemple, une méthode d’application de conversation Impossible passer en tant que paramètre le nom d’utilisateur de la personne à publier un message, comme indiqué ci-dessous.

[!code-csharp[Main](hub-authorization/samples/sample5.cs)]

Ou bien, vous pouvez créer un objet pour représenter les informations d’authentification et transférer cet objet en tant que paramètre, comme indiqué ci-dessous.

[!code-csharp[Main](hub-authorization/samples/sample6.cs)]

Vous ne devez jamais passer les id de connexion d’un client à d’autres clients, comme un utilisateur malveillant peut utiliser pour simuler une demande à partir de ce client.

<a id="authoptions"></a>

## <a name="authentication-options-for-net-clients"></a>Options d’authentification pour les clients .NET

Lorsque vous disposez d’un client .NET, comme une application console, qui interagit avec un concentrateur est limité aux utilisateurs authentifiés, vous pouvez passer les informations d’identification de l’authentification dans un cookie, l’en-tête de connexion ou un certificat. Les exemples de cette section montrent comment utiliser les différentes méthodes pour authentifier un utilisateur. Ils ne sont pas des applications SignalR entièrement fonctionnelles. Pour plus d’informations sur les clients .NET avec SignalR, consultez [concentrateurs API Guide - Client .NET](../guide-to-the-api/hubs-api-guide-net-client.md).

<a id="cookie"></a>

### <a name="cookie"></a>Cookie

Quand votre client .NET interagit avec un concentrateur qui utilise l’authentification par formulaire ASP.NET, vous devez définir manuellement le cookie d’authentification sur la connexion. Vous ajoutez le cookie à la `CookieContainer` propriété sur le [HubConnection](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.client.hubs.hubconnection(v=vs.111).aspx) objet. L’exemple suivant montre une application console qui Récupère un cookie d’authentification à partir d’une page web et ajoute ce cookie à la connexion.

[!code-csharp[Main](hub-authorization/samples/sample7.cs)]

L’application console publie les informations d’identification **www.contoso.com/RemoteLogin** qui peut faire référence à une page vide qui contient le fichier code-behind suivante.

[!code-csharp[Main](hub-authorization/samples/sample8.cs)]

<a id="windows"></a>

### <a name="windows-authentication"></a>Authentification Windows

Lorsque vous utilisez l’authentification Windows, vous pouvez passer des informations d’identification de l’utilisateur actuel à l’aide de la [DefaultCredentials](https://msdn.microsoft.com/en-us/library/system.net.credentialcache.defaultcredentials.aspx) propriété. Vous définissez les informations d’identification pour la connexion à la valeur de DefaultCredentials.

[!code-csharp[Main](hub-authorization/samples/sample9.cs?highlight=6)]

<a id="header"></a>

### <a name="connection-header"></a>En-tête de connexion

Si votre application n’utilise pas les cookies, vous pouvez passer des informations sur l’utilisateur dans l’en-tête de connexion. Par exemple, vous pouvez passer un jeton dans l’en-tête de connexion.

[!code-csharp[Main](hub-authorization/samples/sample10.cs?highlight=6)]

Puis, dans le concentrateur, vérifiez que le jeton d’utilisateur.

<a id="certificate"></a>

### <a name="certificate"></a>Certificat

Vous pouvez passer un certificat client pour vérifier que l’utilisateur. Vous ajoutez le certificat lors de la création de la connexion. L’exemple suivant montre uniquement comment ajouter un certificat client pour la connexion ; Il n’affiche pas l’application console complète. Elle utilise le [X509Certificate](https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.x509certificate.aspx) classe qui fournit plusieurs façons différentes pour créer le certificat.

[!code-csharp[Main](hub-authorization/samples/sample11.cs?highlight=6)]

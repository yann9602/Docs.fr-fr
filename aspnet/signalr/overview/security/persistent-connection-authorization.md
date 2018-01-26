---
uid: signalr/overview/security/persistent-connection-authorization
title: Authentification et autorisation pour les connexions persistantes SignalR | Documents Microsoft
author: pfletcher
description: "Cette rubrique décrit comment appliquer l’autorisation sur une connexion persistante. Pour plus d’informations sur l’intégration de sécurité dans une application SignalR,..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/10/2014
ms.topic: article
ms.assetid: e264677b-9c01-47ec-94f9-3cd8f08f94af
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/security/persistent-connection-authorization
msc.type: authoredcontent
ms.openlocfilehash: d559cfa21f6444b2361fd003b9ce3d2c9c6c57a4
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="authentication-and-authorization-for-signalr-persistent-connections"></a>Authentification et autorisation pour les connexions persistantes SignalR
====================
par [Patrick Fletcher](https://github.com/pfletcher), [Tom FitzMacken](https://github.com/tfitzmac)

> Cette rubrique décrit comment appliquer l’autorisation sur une connexion persistante. Pour obtenir des informations générales sur l’intégration de sécurité dans une application SignalR, consultez [Introduction à la sécurité](introduction-to-security.md). 
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


## <a name="enforce-authorization"></a>Appliquer l’autorisation

Pour appliquer des règles d’autorisation lorsque vous utilisez un [persistentconnection ne](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.persistentconnection(v=vs.111).aspx) vous devez substituer la `AuthorizeRequest` (méthode). Vous ne pouvez pas utiliser le `Authorize` attribut avec des connexions persistantes. Le `AuthorizeRequest` méthode est appelée par l’infrastructure SignalR avant chaque requête pour vérifier que l’utilisateur est autorisé à effectuer l’action demandée. Le `AuthorizeRequest` méthode n’est pas appelée à partir du client ; au lieu de cela, vous authentifiez l’utilisateur via le mécanisme d’authentification standard de votre application.

L’exemple ci-dessous montre comment limiter les demandes aux utilisateurs authentifiés.

[!code-csharp[Main](persistent-connection-authorization/samples/sample1.cs)]

Vous pouvez ajouter une logique d’autorisation personnalisée de la méthode AuthorizeRequest ; par exemple, la vérification si un utilisateur appartient à un rôle particulier.
